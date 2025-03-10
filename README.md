# Boundary Controls

## Objective
Gain hands-on experience deploying essential tools and techniques to bolster network and device security. Explore a range of boundary controls including deterrent, preventive, and detective measures to enhance systems' resilience against potential threats. Learn server-hardening techniques, use Linux built-in firewall (iptables) to regulate traffic flow, and shield servers from common attacks.

## Skills Learned
- Limiting access to shared memory in Linux.
- Limiting SSH login to specific users in Linux.
- Limiting users' ability to change to a different user in Linux.
- Disabling USB drive usage.
- Checking for open ports in Linux.
- Configuring and utilizing iptables firewall in Linux.

## Tools Used
- **Operating Systems**: Ubuntu Linux, Kali Linux
- **Commands**:
  - Shared Memory: `mount`, `cp`, `nano`
  - SSH: `ssh-keygen`, `ssh-copy-id`, `systemctl`
  - User Privileges: `ls`, `groups`, `id`, `dpkg-statoverride`
  - USB Control: `nano`, `udevadm`
  - Network Analysis: `apt-get`, `netstat`, `ufw`
  - Firewall: `iptables`

## Steps

### Part 1 - Limiting Access to Shared Memory
1. Launch Ubuntu Linux machine (Machine A).
2. Open a terminal window.
3. Run: `mount | grep tmpfs | grep shm`
4. Backup fstab file: `sudo cp /etc/fstab /etc/fstab.bak`
5. Edit fstab file: `sudo nano /etc/fstab`
6. Add line at end: `tmpfs /dev/shm tmpfs defaults,rw,nosuid,nodev,noexec 0 0`
7. Save and exit the file.
8. Remount shared memory: `sudo mount -o remount /dev/shm`
9. Verify: `mount | grep tmpfs | grep shm`

### Part 2 - Limiting SSH Login to Specific Users Only
1. Launch Kali Linux machine (Machine B).
2. Open a terminal window.
3. Generate SSH key: `ssh-keygen`
4. Save key in default directory and set a passphrase.
5. Copy key to remote host: `ssh-copy-id username@remotehost`
6. Confirm connection: `yes`, and enter password.
7. Switch to Ubuntu Linux machine (Machine A).
8. Backup sshd_config file: `sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak`
9. Edit sshd_config file: `sudo nano /etc/ssh/sshd_config`
10. Ensure lines are set:
    - `PermitRootLogin no`
    - `PasswordAuthentication no`
11. Add line at end: `AllowUsers username@IP_Address`
12. Save and exit the file.
13. Restart SSH: `sudo systemctl restart ssh`
14. Switch to Kali Linux machine (Machine B).
15. Connect via SSH: `ssh username@remote_host`
16. Enter passphrase for SSH key.
17. Task: Configure to block a user and test SSH connection.
18. Task: Configure to allow users from a specific subnet.
19. Task: Explain why `AllowUsers *@*` is insecure.

### Part 3 - Limiting Users' Ability to Change to a Different User
1. On Ubuntu Linux machine (Machine A).
2. Open a terminal window.
3. Check permissions: `ls -l /bin/su`
4. List groups: `groups`
5. Check user ID: `id`
6. Restrict su command: `sudo dpkg-statoverride --update --add root group 4750 /bin/su`
7. Verify permissions: `ls -l /bin/su`

### Part 4 - Disabling USB Drive Usage
1. On Ubuntu Linux machine (Machine A).
2. Open a terminal window.
3. Create udev rule: `sudo nano /etc/udev/rules.d/10-usbblock.rules`
4. Add code: `ACTION=="add", SUBSYSTEM=="usb", ATTR{bInterfaceClass}=="08", ATTR{bInterfaceSubClass}=="06", ATTR{bInterfaceProtocol}=="50", RUN+="/bin/sh -c 'echo 0 > /sys$devpath/authorized'"`
5. Save and exit the file.
6. Reload udev rules: `sudo udevadm control --reload-rules`
7. Task: Test and observe USB mounting.
8. Task: Detail process to reverse changes.

### Part 5 - Checking What Open Ports You Have
1. On Ubuntu Linux machine (Machine A).
2. Open a terminal window.
3. Install net-tools: `sudo apt-get install net-tools`
4. Get netstat help: `netstat --help`
5. Check active ports: `netstat -antp`
6. Check listening ports: `netstat -tuln`
7. Monitor network: `watch -n 1 netstat -tun`
8. Task: Generate network traffic and observe.
9. Task: Close vulnerable ports using ufw.

### Part 6 - Firewall Setup
1. On Ubuntu Linux machine (Machine A).
2. Open a terminal window.
3. Check iptables version: `sudo iptables --version`
4. List iptables rules: `sudo iptables -L`
5. Configure stateful firewall:
   - `sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT`
6. Allow incoming HTTP traffic:
   - `sudo iptables -A INPUT -p tcp --dport 80 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT`
7. Allow incoming SSH traffic:
   - `sudo iptables -A INPUT -p tcp --dport 22 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT`
8. Add default drop policy: `sudo iptables -A INPUT -j DROP`
9. Block specific IP: `sudo iptables -A INPUT -s IP_Address -j DROP`
10. List rules with line numbers: `sudo iptables -L --line-numbers`
11. Delete a rule: `sudo iptables -D chain_type rule_number`
12. Flush rules: `sudo iptables -F`
13. Task: Test effects of changes from another VM.
14. Task: Create and apply rules for open ports.
15. Task: Research and detail process to save iptables rules permanently.
