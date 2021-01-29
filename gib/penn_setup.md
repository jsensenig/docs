# DUNE Timing Software Setup

The GPS Interface Board (GIB) uses the Enclustra PM3 base board and AX3 FPGA with a custom FMC board designed by UPenn.

### 1. Software Setup

Connect the power and the JTAG programming cable. Vivado can then be used to program the FPGA using the Vivado hardware manager. Software under development can be found on [gitlab][https://gitlab.cern.ch/protoDUNE-SP-DAQ/timing-board-software/-/blob/feature/gib_debug].

### 2. Virtual Machine Setup

If no CentOS machine is available one can also use a virtual machine.  Use the *VirtualBox* VM [here](https://drive.google.com/file/d/1mEOvxuIf1N9In-1E9WzR71tCw3JEfbCF/view?usp=sharing) if a system is not already available. Ask if you need the password for the VM. Note: The mouse can be freed from the VM on Linux with the right-side Ctrl key.

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
