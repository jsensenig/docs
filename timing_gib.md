# DUNE GPS Interface Board

The GPS Interface Board (GIB) uses the Enclustra PM3 base board and AX3 FPGA with a custom FMC board designed by UPenn.

### 1. Programming the board

Connect the power and the JTAG programming cable. Vivado can then be used to program the FPGA using the Vivado hardware manager. Firmware under development on [gitlab][https://gitlab.cern.ch/protoDUNE-SP-DAQ/timing-board-firmware/-/tree/gib_debug].

### 2. Configuring the Board

Now that the FPGA is programmed we are ready to configure it. Use the *VirtualBox* VM [here](https://drive.google.com/file/d/1mEOvxuIf1N9In-1E9WzR71tCw3JEfbCF/view?usp=sharing) if a system is not already available. Ask if you need the password for the VM. Note: The mouse can be freed from the VM on Linux with the right-side Ctrl key.

When the system is powered open a terminal and we need to establish a connection to the FPGA so we can r/w to the registers. First establish the connection to the IP address,
```
sudo ifconfig up enps03 192.168.200.1
```
then  ping the IP address `192.168.200.16` to make sure the connection is made,
```
ping 192.168.200.16
```
if the connection is still not made, retry the `ifconfig` step.

When the connection is established the board can be configured with a debug
script (software library for control is under construction).

The order of configuration is as follows:
1. *Reset*
- Reset the I$^i{2}$C switch at `0x70` (AL)
- Reset the 2 I$^i{2}$C expanders at `0x75` and `0x74` (AL)
- Reset the clock generator (SI5395) at `0x68` (AL)

2. *Configure*
- GPS clock translator
  - Enable translator `io.csr.ctrl.gps_clk_en` (AL)
  - Set noise filter A, B to full bandwidth mode `0x0` `io.csr.ctrl.gps_clk_fltr_a` and `io.csr.ctrl.gps_clk_fltr_b`
- I2C FPGA switch (Enclustra AX3/PM3)
  - Write [`0x1`, `0x7F`] to register `0x21`



