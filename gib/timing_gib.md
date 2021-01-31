
### Configuration: I2C Registers

The I2C tree for the GIB is as follows,
* AX3 Switch (Addr. 0x40)
    * GIB Switch (Addr. 0x70)
        * Channels 1 - 7 (All configurable components)
             
The I2C channels and register addresses on the GIB are,

| Component         | I2C Channel    | Address |
| :---------------: | :-------------:| :------:| 
| Temp. Monitor     | 0              | 0x48    |   
| EEPROM            | 0              | 0x50    |
| SI5395 Clock Gen. | 0              | 0x68    |
| I2C Bus Extender 1| 0              | 0x74    |
| I2C Bus Extender 2| 0              | 0x75    |
| Power Monitor 5.0V| 0              | 0xCE    |
| Power Monitor 5.0V| 0              | 0xCE    |
| Power Monitor 3.3V| 0              | 0xD0    |
| Power Monitor 2.5V| 0              | 0xD2    |
| Power Monitor 1.8V| 0              | 0xD4    |
| SFP 1 EEPROM      | 1              | 0x50    |
| SFP 1 DIAG        | 1              | 0x54    |
| SFP 2 EEPROM      | 2              | 0x50    |
| SFP 2 DIAG        | 2              | 0x54    |
| SFP 3 EEPROM      | 3              | 0x50    |
| SFP 3 DIAG        | 3              | 0x54    |
| SFP 4 EEPROM      | 4              | 0x50    |
| SFP 4 DIAG        | 4              | 0x54    |
| SFP 5 EEPROM      | 5              | 0x50    |
| SFP 5 DIAG        | 5              | 0x54    |
| SFP 6 EEPROM      | 6              | 0x50    |
| SFP 6 DIAG        | 6              | 0x54    |


The reset and configuration of the board are in the order as follows:
1. Resets
   1. Firmware reset
   2. I2C Switch, I2C Bus Extender, Clock Generator
2. Wake Enclustra AX3 I2C switch.
3. Configure GPS clock translator.
4. Configure clock generator (SI5395).
5. Enable SFPs.
6. Select recovered clock edge on which the recovered data is captured.

The entry in the `connections.xml` file is "GIB_PRIMARY", for example in python
``` python
lDeviceName = "GIB_PRIMARY"
hw = lConnectionManager.getDevice(str(lDeviceName))
```


Additional functions are,

1. I2C Switch Channel Select

``` python
def I2CSwitch(hw, ch, off=False):

  channel = (1 << ch) & 0x7F

  if (off): channel = 0x0

  SwI2C = I2CCore(hw, 10, 5, "io.i2c", None)

  # Select I2C channel 0 - 6
  SwI2C.write(0x70, [channel], True)

  print("Selected I2C channel:", ch)
  time.sleep(0.1)
  print( "Read switch register", hex( SwI2C.read(0x70,1)[0] ) )
```

2. Read temperature monitor

``` python
def TempMonitor(hw):

  # Select the correct I2C bus
  I2CSwitch(hw, 0)

  lI2CBusNode = hw.getNode("io.i2c")
  if( not lI2CBusNode.ping(0x48) ):
    print("Could not find Temp. Monitor at address 0x48")
    return

  TempI2C = I2CCore(hw, 10, 5, "io.i2c", None)

  # Select temp register and read it
  TempI2C.write(0x48, [0x0], True)
  temp = TempI2C.read(0x48, 2)

  # TODO Add config and trip point regs

  print("Temperature byte 1 ", temp[1], " byte 0 ", temp[0])
  
  # Temp    D15 | D14 | D13 | D12 | D11 | D10 | D9 | D8 | D7 | D6 | D5 | D4 | D3 | D2 | D1 | D0
  # format  MSB | b7  |  b6 |  b5 |  b4 |  b3 | b2 | b1 | LSB| X  | X  | X  | X  | X  | X | X 

  # Assuming TempI2C.read*0x48, 2) returns a list of bytes ordered as [MSB, ..., LSB] 
  t_lsb = (temp[0] >> 7) & 0x1 # temp = [MSByte,LSByte]
  t_msB = temp[1] & 0xFF       # temp = [MSByte,LSByte]

  temp_res = ( t_msB << 1 ) + t_lsb

  print("Temperature = ", temp_res * 0.5) # 1b = 0.5C
```

3. Read Power Monitor

``` python
def PwrMonitor(hw):

  # Select the correct I2C bus
  I2CSwitch(hw, 0)

  pmons = {'5_0v' : 0xCE, '3_3v' : 0xD0, '2_5v' : 0xD2, '1_8v' : 0xD4}

  lI2CBusNode = hw.getNode("io.i2c")
  for m in pmons:
    if( not lI2CBusNode.ping( pmons[m]) ):
      print("Could not find Power Monitor at address", pmons[m])
      return

  PwrI2C = I2CCore(hw, 10, 5, "io.i2c", None)
  lI2CBusNode = hw.getNode("io.i2c")

  # Check all monitors are online
  for m in pmons:
    print(m, " Power monitor online ", lI2CBusNode.ping(pmons[m]))
```
