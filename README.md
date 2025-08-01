# SyncDaemon
Simple Linux daemon that outputs synchronization pulses on parallel port.

## Overview
This is a simple binary that is supposed to run as a Linux daemon. Every ten
seconds it outputs through the parallel port a sequence of pulses that
encode the time of computer's real time clock. That way any device that can
record the parallel port output (like EEG or MEG recording device) can be
synchronized to the computer. For more details see [this paper](https://doi.org/10.1016/j.mex.2018.01.002).

## Installation
The installation instructions below are for Ubuntu 24.04. They might or might not work on other systems.

### Find out your parallel port address
Run  
    
    sudo dmesg | grep parport  

You should see something like  

    [    3.317179] parport0: PC-style at 0x2008 (0x2000), irq 144
    [    3.425113] lp0: using parport0 (interrupt-driven).

The parallel port address here is 0x2008. SyncDaemon needs the address in decimal,
and in decimal it's 8200 (8200 = 0x2008).  

### Install SyncDaemon binary
Put the `SyncDaemon` binary somewhere in your system. Let's assume it's
`/opt/SyncDaemon/SyncDaemon`.


Run
    
    sudo /opt/SyncDaemon/SyncDaemon 8200 255

Here the first parameter is the port number and the second parameter specifies
which bits are used to output the timing signal. The value 255 tells SyncDaemon
to output the timing pulses on all the eight bits of the parallel port.

Check that:  

 1. SyncDaemon produces no error messages  
 2. after a short period of time (less than a few minutes) it starts outputting timing pulses (a train of pulses every 10 seconds) to the parallel port  

If the above works, configure the system to run SyncDaemon automatically at the startup.

### Configure SyncDaemon to run automatically
You need to create a systemd unit for running SyncDaemon automatically at the system startup.  

 1. Modify the `syncdaemon.service.example` file according to your computer's setup. You basically need to make sure that the path to the `SyncDaemon` binary and port number are correct.
 2. Rename the file to `syncdaemon.service` and copy it to `/etc/systemd/system/`.
 3. Configure systemd to start the service automatically on the system startup:  
    
    `systemctl enable syncdaemon`

Now SyncDaemon should run automatically when the computer starts. Note that you can also start and stop it manually using
    
    systemctl start syncdaemon
    systemctl stop syncdaemon

You can also check the daemon's status with
    
    systemctl status syncdaemon


