
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


The resets for the board are the following, in this order,
``` python
# Reset I2C bus switch
io.csr.ctrl.rst_i2c_sw.write(1)

# Reset I2C bus extender 0 and 1
io.csr.ctrl.rst_i2c_extndr_0.write(1)
io.csr.ctrl.rst_i2c_extndr_1.write(1)

# Reset SI5395 clock generator
io.csr.ctrl.rst_clk_gen.write(1)

# De-assert the reset
io.csr.ctrl.rst_i2c_sw.write(0)
io.csr.ctrl.rst_i2c_extndr_0.write(0)
io.csr.ctrl.rst_i2c_extndr_1.write(0)
io.csr.ctrl.rst_clk_gen.write(0)
```

The components which require set up are GPS clock translator, 
``` python
# Enable GPS clock translator
io.csr.ctrl.gps_clk_en.write(1)

# Set the bandwidth filters to "full bandwidth" mode, A = B = 0
io.csr.ctrl.gps_clk_fltr_a.write(0)
io.csr.ctrl.gps_clk_fltr_b.write(0)
```

