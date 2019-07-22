

### Set docker storage do diffrent location

https://stackoverflow.com/questions/32070113/how-do-i-change-the-default-docker-container-location


Stop docker daemon:

```
$ systemctl stop docker
```

Make a new config file: `/etc/docker/daemon.json`

```
{
  "graph": "/data/docker"
}
```

Start docker:
```
$ systemctl start docker
```