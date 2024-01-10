# ISSUE
In short, SQLite is garbage on NFS mounts and will lock up causing major performance issues. More info [here][L1].


## Workaround
Services with SQLite databases like Radarr, Sonarr, etc. need to run on the localsystem disk. 

The NFS mount & local filesystem folder structure layout including soft-links:

```bash
~$ cat /etc/fstab

## Media 
 192.168.166.1:/mnt/pool.0.z3_multimedia/media  /mnt/media	nfs defaults 0	0

## Pirate
 192.168.166.1:/mnt/pool.1.ssd_data/pirate      /mnt/pirate	nfs defaults 0	0

~$ ls -alh /mnt

    drwxr-xr-x media media /mnt/
    drwxr-xr-x media media ├── media/
    drwxr-xr-x media media |   └── videos/
                           |       └── tv
                           └── pirate/
    -rw-rw-r-- media media      ├── docker-compose.yml
    drwxr-xr-x media media      └── tv

~$ ls -alh /opt/docker/{.pirate,pirate}

    drwxr-xr-x media media /opt/
    drwxr-xr-x media media └── docker/
    drwxr-xr-x media media     ├── .pirate/
    drwxr-xr-x media media     |   └── tv
    drwxr-xr-x media media     |       ├── config/
    -rw-r--r-- media media     |       ├── .env
    -rw-r--r-- media media     |       ├── README.md
    -rw-r--r-- media media     |       └── sonarr.yaml
                               |
    lrwxrwxrwx media media     └── pirate/ -> /mnt/pirate
    -rw-r--r-- media media         ├── docker-compose.yml
    drwxr-xr-x media media         └── tv
    drwxr-xr-x media media             ├── backups
    lrwxrwxrwx media media             ├── config -> /opt/docker/.pirate/tv/config
    lrwxrwxrwx media media             ├── downloads -> /mnt/media/downloads
    lrwxrwxrwx media media             ├── .env -> /opt/docker/.pirate/tv/.env
    -rw-r--r-- media media             ├── README.md
    lrwxrwxrwx media media             ├── sonarr.yaml -> /opt/docker/.pirate/tv/sonarr.yaml
    lrwxrwxrwx media media             └── tv -> /mnt/media/videos/tv
```

[L1]: https://www.sqlite.org/faq.html#q5