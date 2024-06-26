# Installation

Requires:
- [ssh](../docs/setup-ssh.md)
- [docker-compose](../docs/install-docker-compose.md)

## Directory Configuration
You can create a **Folder** using the **File Manager** app in UGOS. It can be either a **Shared Folder** or a **User Folder**, depending on how you want the contents to be accessible **outside** of **Plex** (from the filesystem). For accessing through **Plex**, it doesn't matter, as only **Plex** application needs access to the contents of the directory.

**Shared Folders** and **User Folders** will be indexed by UGOS's **Indexing service**, increasing **disk activity** and **CPU load**. Alternativey, you can create a directory from the terminal, outside of the **Shared Folders** and **User Folders** space, and it will not be indexed.

### Shared and User Folder
Create a Shared or User folder where you wish

### Custom Directory
To create a `data` directory, login to the unit with `ssh` and run:
```sh
# create directory for plex contents
sudo mkdir -p \
    /volume1/data/plex/config \
    /volume1/data/plex/data \
    /volume1/data/plex/transcode
```

Change the directory ownership to the logged in user, instead of `root` user:

```sh
sudo chown -R $(id -u):$(id -g) /volume1/data/
```

## UID and GID Configuration
When Plex runs, it will need read+write access to the directories you configured. You need to set the environment variabes `UID` and `GID` so plex container will have the same permissions as this user.

On an `ssh` session, you can check the current user's UID and GID with `id` command:
```sh
amdin@NAS:~$ id -u
1001

admin@NAS:~$ id -g
10
```

You can check all users' UID and GID in `/etc/passwd` file:

```sh
$ cat /etc/passwd
```

```sh
root:x:0:0:root:/root:/bin/sh
# ...
admin:x:1001:10:UGREEN USER:/home/admin:/bin/bash
```
Set `UID` and `GID` `environment` variables in [docker-composer.yaml](./docker-composer.yaml), so **plex** will run with the necessary permissions.

## Run
When the containers are created using docker-compose, they will also be displayed on UGOS's Docker app, and will start automatically when the app start (unless manually stopped, as per the configuration provided).

Place the [docker-compose](./docker-compose.yaml) file inside the folder/directory created for Plex, and run the command from within that directory:
```sh
admin@NAS:~$ cd /volume1/data/plex
admin@NAS:/volume1/data/plex$ ls -l
total 4
drwxr-xr-x 1 admin admin  40 Jun 19 21:40 config
drwxr-xr-x 1 admin admin  72 Jun 21 17:40 data
-rwx------ 1 admin admin 413 Jun 25 22:01 docker-compose.yml
drwxr-xr-x 1 admin admin  18 Jun 26 16:39 transcode
```

Start the service (requires `root`)
```sh
sudo docker-compose up -d
```

You can check the initialization logs with:
```sh
sudo docker logs -tf plex
```

## Access
When the service is ready, it can be accessed from the local network at:
- `http://<nas-device-ip-addr>:32400/`