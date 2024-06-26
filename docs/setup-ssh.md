# Setup SSH

Enable SSH on UGOS interface under Control Panel / Terminal / SSH -> `Enable` and Apply the configuration.

From a computer on the nework, setup an ssh-capable client and connect with:
```sh
ssh <admin-user>@<nas-device-ip-addr>
```

### root privileges
The administrator users have `root` privileges. Commands can be run with `sudo <command>`, or you can start a `root` session with `sudo su -`:
```sh
admin@NAS:~$ sudo su -
Password: ******

BusyBox v1.35.0 (Debian 1:1.35.0-4+b3) built-in shell (ash)
Enter 'help' for a list of built-in commands.

root@NAS:~#
``` 