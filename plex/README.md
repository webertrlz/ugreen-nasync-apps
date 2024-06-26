# Installation

Requires:
- [ssh](../docs/setup-ssh.md)
- [docker-compose](../docs/install-docker-compose.md)

## Folder Configuration
You can create a **Folder** using the **File Manager** app in UGOS. It can be either a **Shared Folder** or a **User Folder**, depending on how you want the contents to be accessible **outside** of **Plex** (from the filesystem). For accessing through **Plex**, it doesn't matter, as only **Plex** application needs access to the contents of the directory.

**Shared Folders** and **User Folders** will be indexed by UGOS's **Indexing service**, increasing **disk activity** and **CPU load**. Alternativey, you can create a directory from the terminal, outside of the **Shared Folders** and **User Folders** space, and it will not be indexed. This is an **advanced** setup that requires knowledge of linux systems and is explained in a separate [Advanced Configuration](#advanced-configuration) section below.

### Plex Folder
You can create a new **Shared Folder**, or create a regular **Folder** inside of an existing **Shared Folder** or **User Folder**, where you wish to place the contents of Plex, and according to your own preferences of which users of the system (NAS, not Plex) will have direct access to the contents of the folders:
- Media (films, TV series, etc)
- Plex Configuration (local database)
- Computed files (transcoded media).

The Folder should be created using the **File Manager** app in UGOS. 

This option will not support **hardware transcoding** as it requires mounting a device that is not available using the Docker interface (`/dev/dri`)

## UID and GID Configuration
When Plex runs, it will need read+write access to the directories you configured. You need to set the environment variabes `UID` and `GID` so plex container will have the same permissions as this user. Unfortunately you cannot see this information directly in UGOS interface, so you need to retrieve it from an `terminal` session.

On an `terminal` session (`ssh`), you can check the current user's UID and GID with `id` command:
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
To run Plex, you can create the container using the Docker app in UGOS, but you can only map Shared and User Folders as volumes to the container. This is the easiest way as it doesn't require `docker-compose`.

Check the configuration attributes in [docker-compose](./docker-compose.yaml) file and create the container with the same parameters.

## Access
When the service is ready, it can be accessed from the local network at:
- `http://<nas-device-ip-addr>:32400/`

## Advanced Configuration
This configuration is for savvy users that are comfortable with using `ssh`, `shell` commands, `vim` and `rsync`.

### Custom Directory
To create a `data` directory (example), login to the unit with `ssh` and run:
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

### Docker Compose
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

## Add Media
Custom directories that are not in Shared Folders or User Folders are not directly accessable using UGOS' File services (SMB, NFS). To add media to the custom directry, you will need to:
- Transfer files to one of the Shared / User folders you can access through regular File services protocols
- Login with `ssh` and `mv`* the files to the correct destination.

\*`mv` command will be instant as long as the files stay inside the same `volume`.