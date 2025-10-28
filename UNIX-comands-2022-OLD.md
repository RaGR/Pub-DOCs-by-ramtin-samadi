## LINUX (Ubuntu) CLI usefull commands list


```bash
pwd = Present Working Directory
chmod:
Read Write Execute
Group - o(U)wner - wOrld
+ -
sudo chmod 777 [file_name]

sudo dpkg-repack package_name
((then chmod from /usr/home))

apt list --installed
pwd = present working directory
sudo apt install tree
tree = show full map of system files and dirs
mkdir = make directory



sudo apt full-upgrade
sudo apt upgrade nautilus

mounting:
	sudo apt update
	sudo apt install ntfs-3g
	sudo ntfsfix /dev/sdd1
identify disk:
	lsblk
	sudo mkdir /media/usb
	sudo mount -t ntfs-3g /dev/sdd1 /media/usb
	ls /media/usb
	
	sudo umount /media/usb
Replace /dev/sdX1 with the actual device identifier for your USB drive.

specefic mount
    sudo mount -t ntfs-3g -o uid=$(id -u),gid=$(id -g) /dev/sdX1 /media/usb

##GRAPHICAL USER INTERFACE "MOUNT POINT" = media !!



sudo systemctl restart NetworkManager


FLUSH DNS:
1)
sudo apt install systemd-resolved
sudo systemd-resolve --flush-caches
2)
sudo resolvectl flush-caches


copy cmnd:
sudo cp -r Telegram_(Path) /opt
sudo cp Telegram_(Path)/* /opt
* = select all files and directories containing mother dir
move = mv
sudo mv Telegram /opt
~ = HOME directory path
/ = ROOT directory path
======================CRACK==============================
/opt/ja-netfilter-202x/ja-netfilter/scripts

sudo chmod +x install.sh
sudo chmod +x ./install.sh
./install.sh

sudo systemctl stop <service_name> stops the service immediately.
sudo systemctl disable <service_name> prevents the service from starting on boot.
LIKE MYSQL FOR INSTANCE (mysql)
start again:
https://ubuntu.com/server/docs/install-and-configure-a-mysql-server

lunching xampp:
sudo /opt/lampp/manager-linux-x64.run
sudo /opt/lampp/lampp start
file Directory:
/opt/lampp

sudo /opt/lampp/share/xampp-control-panel/xampp-control-panel

ساخت اپ آیکون:
sudo nano /usr/share/applications/xampp.desktop

[Desktop Entry]
Version=1.0
Type=Application
Name=XAMPP
Exec=sudo /opt/lampp/lampp start
Icon=/opt/lampp/lampp.png
Terminal=true
Categories=Development;

sudo chmod +x /usr/share/applications/xampp.desktop
```

## Linux Network Management Commands Cheat Sheet

| Command | Description |
|---|---|
| `ifconfig` | Display/configure network interfaces (older) |
| `ip` | Manage IP addresses, routing, interfaces (newer) |
| `ping` | Test reachability to a host |
| `traceroute` | Display path to a destination |
| `mtr` | Combine ping and traceroute |
| `netstat` | Display network connections, routing |
| `ss` | Display network socket statistics |
| `dig` | Flexible DNS query tool |
| `nslookup` | Interactive DNS query tool |
| `route` | Manage the IP routing table |
| `arp` | Display/manipulate ARP cache |
| `hostname` | Display/set the system's hostname |

**Note:** Some commands may require root privileges.

This cheat sheet provides a concise overview of common Linux network management commands. For more detailed information and usage examples, refer to the manual pages for each command (e.g., `man ping`, `man ifconfig`).


**nmcli Commands**

* **General**
    * `nmcli general status`: Show overall NetworkManager status
    * `nmcli general hostname`: Get/set system hostname
    * `nmcli general permissions`: Show NetworkManager permissions
    * `nmcli general logging`: Show/set NetworkManager logging level
    * `nmcli general reload`: Reload NetworkManager configuration

* **Device Management**
    * `nmcli dev status`: Show network device status
    * `nmcli dev connect <device>`: Connect to a network device
    * `nmcli dev disconnect <device>`: Disconnect from a network device
    * `nmcli dev wifi list`: List available Wi-Fi networks
    * `nmcli dev wifi connect <SSID>`: Connect to a Wi-Fi network

* **Connection Management**
    * `nmcli con show`: Show all connections
    * `nmcli con show <connection name>`: Show details of a specific connection
    * `nmcli con add <type> [options]`: Create a new connection
        * Examples:
            * `nmcli con add type wifi con-name "MyWifi" ssid "MyWifiSSID" password "MyWifiPassword"`
            * `nmcli con add type ethernet ifname <interface>`
    * `nmcli con mod <connection name> [property] <value>`: Modify a connection
        * Examples:
            * `nmcli con mod "MyWifi" ipv4.addresses "192.168.1.100/24"`
            * `nmcli con mod "MyWifi" ipv4.dns "8.8.8.8,8.8.4.4"`
    * `nmcli con up <connection name>`: Activate a connection
    * `nmcli con down <connection name>`: Deactivate a connection
    * `nmcli con del <connection name>`: Delete a connection

**Note:**

* `<connection name>` refers to the name of the connection you want to manage.
* `<device>` refers to the network device name (e.g., `wlan0`, `eth0`).
* `<SSID>` refers to the SSID of the Wi-Fi network.
* `<interface>` refers to the network interface name.

This list provides a general overview of common `nmcli` commands. For detailed usage and available options, refer to the `nmcli` man page (`man nmcli`).

