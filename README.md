# Bluetooth-RPi-Python
A Python script running on Raspberry Pi 3 to receive commands (data) from / send executed results to an Android Device over bluetooth. Bluetooth-RPi-Python is used with this Android App: [Bluetooth-RPi](https://github.com/Yurockkk/Bluetooth-RPi) 

## Instruction

### Step 0 - Install the bluetooth softwares
Open terminal, install softwares:

`sudo apt-get update`

`sudo apt-get install bluez bluez-utils minicom`

### Step 1 - Setup the SPP (serial port profile) 
Open terminal, edit this file

`sudo nano /etc/systemd/system/dbus-org.bluez.service`

Add `-C` at the end of the `ExecStart=` line, to start the bluetooth daemon in 'compatibility' mode. Add a new `ExecStartPost=` immediately after that line, to add the SP Profile. The two lines should look like this:

`ExecStart=/usr/lib/bluetooth/bluetoothd -C`

`ExecStartPost=/usr/bin/sdptool add SP`

### Step 2 - Automatically listen to hci0 channel and run this python code
Create a new service unit file:

`sudo nano /etc/systemd/system/rfcomm.service`

Edit the file:
```
[Unit]
Description=RFCOMM service
After=bluetooth.service
Requires=bluetooth.service

[Service]	
ExecStart=/usr/bin/rfcomm watch hci0 &; /usr/bin/python2 /home/pi/DIRECTORY_TO_YOUR_PYTHON_CODE/bluetooth_terminal_Yubo.py	

[Install]
WantedBy=multi-user.target

```

**NOTE:** There is only **one line of code** in **[Service]** section and there is a **space** between `bin/python2` and /home

Save it, Enable the service unit file:

`sudo systemctl enable rfcomm`

### Step 3 - Add the bash command `hciconfig hci0 piscan` in the file “/etc/rc.local” (before the last line of “exit 0”):

`sudo nano /etc/rc.local`

![](images/rc.png)

### Step 4 - Set the “discoverableTimeout” to “0” in the file “/etc/bluetooth/main.conf”:
`sudo nano /etc/bluetooth/main.conf`

![](images/main.png)

### Step 5 - Reboot your RPi
Now, your RPi is able to receive commands (data) from / send executed results to an Android device.

