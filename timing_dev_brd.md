# DUNE Timing Dev Board

The timing dev board uses the Enclustra base board and FPGA with an FMC card designed by Bristol University. The control computer needs CentOS7 but there was a computer with Ubuntu so there is a virtual machine (VM) running CentOS7.

### 1. Programming the board

Connect the power and the JTAG programming cable. Vivado can then be used to program the FPGA using the Vivado hardware manager. The bit file can be downloaded [*link here*] . When the FPGA has been successfully programmed the *DONE* LED should illuminate.

### 2. Configuring the Board

Now that the FPGA is programmed we are ready to configure it. Use the *VirtualBox* VM ([*link to VM image*]) if a system is not already available. 

When the system is powered open a terminal and we need to establish a connection to the FPGA so we can r/w to the registers. First establish the connection to the IP address,
```
sudo ifconfig up enps03 192.168.200.1
```
then  ping the IP address `192.168.200.16` to make sure the connection is made,
```
ping 192.168.200.16
```
if the connection is still not made, retry the `ifconfig` step.

When the connection is established we need to initilaize the control. software called `pdtbulter`. Source the control software using both enviroment scripts,
```
source timing-board-software/tests/env2.sh
source timing-board-software/tests/env.sh
```
Now the software should be available to use and the board can be reset using,
```
pdtbulter mst DUNE_FMC_PRIMARY part-0 reset
```
If this throws errors check again the connection to the IP address is stil there. If there are no errors the board should be generating a 250Mhz clock and we're ready to configure the board using,
```
pdtbulter mst DUNE_FMC_PRIMARY part-0 configure
```
Now everything is set up and the board should be generating a 250Mhz clock and data with a valid timestamp.
