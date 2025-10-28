To enable DHCP for an Ethernet card on Ubuntu, you can follow these steps depending on the version of Ubuntu you are using. The process involves modifying network configuration files.

### For Ubuntu 17.10 and Later (Using Netplan)

1. **Open Terminal**: Press `Ctrl + Alt + T` to open the terminal.

2. **Locate the Netplan Configuration File**: Typically, the configuration files are located in `/etc/netplan/`. You can list the files with:
   ```bash
   ls /etc/netplan/
   ```

3. **Edit the Configuration File**: Open the relevant YAML file (e.g., `01-netcfg.yaml`) using a text editor like `nano` or `vim`:
   ```bash
   sudo nano /etc/netplan/01-netcfg.yaml
   ```

4. **Configure DHCP**: Modify or add the following lines to enable DHCP for your Ethernet interface (replace `eth0` with your actual interface name if different):
   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       eth0:
         dhcp4: true
         dhcp6: false
   ```

5. **Save and Exit**: If you are using `nano`, press `Ctrl + O` to save and `Ctrl + X` to exit.

6. **Apply the Configuration**: Run the following command to apply the changes:
   ```bash
   sudo netplan apply
   ```

### For Ubuntu 16.04 and Earlier (Using /etc/network/interfaces)

1. **Open Terminal**: Open your terminal.

2. **Edit the Interfaces File**: Open the `/etc/network/interfaces` file in a text editor:
   ```bash
   sudo nano /etc/network/interfaces
   ```

3. **Add or Modify DHCP Configuration**: Modify or add the following lines for your Ethernet interface (replace `eth0` with your actual interface name):
   ```plaintext
   auto eth0
   iface eth0 inet dhcp
   ```

4. **Save and Exit**: Save your changes and exit the editor.

5. **Restart Networking Service**: To apply the changes, restart the networking service with:
   ```bash
   sudo systemctl restart networking
   ```
   Or for older versions:
   ```bash
   sudo service networking restart
   ```

### Verify DHCP Configuration

After applying these changes, you can verify that your Ethernet interface is using DHCP by checking its IP address:

```bash
ip addr show eth0  # Replace eth0 with your interface name if different.
```

You should see an IP address assigned from your DHCP server.

By following these steps, you will successfully enable DHCP for your Ethernet card on Ubuntu.

Citations:
[1] https://geek-university.com/configure-dhcp-client-on-ubuntu/
[2] https://www.server-world.info/en/note?os=Ubuntu_23.04&p=dhcp&f=2
[3] https://unix.stackexchange.com/questions/319740/use-dhcp-on-eth0-using-command-line
[4] https://www.alibabacloud.com/help/en/ecs/how-to-configure-the-network-as-dhcp-in-a-linux-image
