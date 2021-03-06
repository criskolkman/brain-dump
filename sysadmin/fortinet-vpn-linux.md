# Fortinet SSL VPN on Linux

Fortinet's proprietary Linux client doesn't work well on Debian 9.

- Compile from source for a non-proprietary [CLI version](https://github.com/adrienverge/openfortivpn)
- Non-proprietary [GUI version](https://github.com/theinvisible/openfortigui) or [apt installation](https://apt.iteas.at/)

## GUI Installation
These instructions are for Debian 9.  For Ubuntu, check the [developer's blog](https://hadler.me/linux/openfortigui/).

Add the developer's signing key
```bash
apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 2FAB19E7CCB7F415
```

Add the developer's repo to apt's sources list
```bash
echo "deb https://apt.iteas.at/iteas stretch main" > /etc/apt/sources.list.d/iteas.list
```

### It Has to be ran as root or sudo

```bash
sudo openfortigui
```

Or be lazy and set a desktop shortcut via the Menu Editor
- Super user key (windows key)
- Search 'Menu Editor' (go to the software center & install one if its not currently installed)
- Mine installed under 'Internet' > click it > Properties
- Under 'Command' > change from `/usr/bin/openfortigui` to: `sudo /usr/bin/openfortigui`

When you go to use the application, you'll be prompt for a password.

### Get rid of the password prompt

As root, run:
```bash
visudo
```

Find:
```bash
root    ALL=(ALL:ALL) ALL
```

Add *beneath* (replace angela for your username):
```bash
angela    ALL=(ALL) NOPASSWD: /usr/bin/openfortigui
```

Find OpenFortiGui in your application menu and click it, it'll auto launch as sudo with zero additional steps.

### Worth noting
- If you enable the sudo launch *after* you have connections configured as a normal user, you might jag up your connections, as the AES key that encrypts the passwords will be registered to another user.  If you can't log back onto them, delete the connections and re-add them.

- If you have a chattr lock on /etc/resolv.conf, this application will not load the VPN's DNS resolvers.
```bash
chattr -i /etc/resolv.conf
```
to unlock it.  (Which also gives Network Manager the ability to fiddle with it, again.)


### (Optional) CLI Logon
```bash
sudo /usr/bin/openfortigui --start-vpn --vpn-name NameOfMyConnection --main-config '/home/angela/.openfortigui/main.conf'
```

### Troubleshooting
I had an issue where I was getting a segfault:
```text
debian kernel: [  573.164599] traps: openfortigui[30174] general protection ip:558fd01e9ed4 sp:7fff8bd2c658 error:0
```

- Logs were empty
- Debug enabled

Turns out, I had changed my password days before and had forgotten -- OpenFortiGUI had my old password saved in the keyring.  I simply updated it via OpenFortiGUI and was able to connect.
