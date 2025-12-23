# Upgrade to RPi4

i wanted to upgrade to an RPi4 since the BTT Pi was not recieving system updates anymore. 

## Enable Can interface on RPi4 with Debian GNU/Linux 13 (trixie)

1. Install tools: sudo apt update && sudo apt install can-utils iproute2.
2. Create a script for startup: Create a script, e.g., ```/usr/local/bin/can-startup.sh```:
  ```
  #!/bin/bash
  # Bring down any existing interface
  ip link set can0 down
  # Set interface to CAN type and bitrate (e.g., 1Mbit)
  ip link set can0 type can bitrate 1000000 loopback off # Or 125000, etc.
  # Bring the interface up
  ip link set can0 up
  ```
3. Make it executable: ```sudo chmod +x /usr/local/bin/can-startup.sh```
5. Use systemd to run on boot: Create /etc/systemd/system/can0.service
  ```
  [Unit]
  Description=Enable CAN Bus Interface
  After=network.target
  
  [Service]
  Type=oneshot
  ExecStart=/usr/local/bin/can-startup.sh
  RemainAfterExit=yes
  
  [Install]
  WantedBy=multi-user.target
  ```

5. Enable the service:
```
sudo systemctl enable can0.service
```
and start it
```
sudo systemctl start can0.service
```
6. Verify: Check the interface:
 ```
ip link show can0
 ```
should show UP, and 
```
dmesg | grep can0
```
should show successful initialization. 

## SB2209 
I needed to flash katapult again to the SB2009 following the guide of Eric Zimmerman
https://github.com/EricZimmerman/VoronTools/blob/main/EBB_CAN.md

better to use the new guide (also Eric is referring to it!)
[https://github.com/EricZimmerman/VoronTools/blob/main/EBB_CAN.md](https://canbus.esoterical.online/)
and follow the new guide.
