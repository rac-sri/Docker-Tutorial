# Docker Tutorial

### *Below Code is Never gonna be used* 

#### chroot : to make a folder act as a root, giving an illusion of another system completely.

```
1. mkdir <dirname>
2. cd <dirname>
3. chroot . bash //error cause of missing dependencies
4. mkdir bin
5. cp /bin/bash bin/
6. Copy all dependecies(in respected directory structure) by getting there location using ldd bin/bash.
7. mkdir lib{,64} // and now copy the dependecies from setp 6
8. Similarly copy other commands and follow the older steps, eg.  /bin/ls
9. chroot . bash // need super user priviledge
```

**Alternatively:** *Plus add Namespaces*
```
1. apt-get install debootstrap
2. debootstrap --variant=minbase bionic(or any other os) <path>  [Reference](https://gist.github.com/varqox/42e213b6b2dde2b636ef)
3. echo "mount -t proc none /proc # process namespace
    mount -t sysfs none /sys # filesystem
    mount -t tmpfs none /tmp # filesystem" >> /better-root/mounts.sh
4. unshare --mount --uts --ipc --net --pid --fork --user --map-root-user chroot /better-root bash   // to make the processes be hidden by other chroot
```

**cgroups: Limit Resources**

```
1. apt-get install -y cgroup-tools htop
2. cgcreate -g cpu,memory,blkio,devices,freezer:/sandbox
3. ps aux
   -cgclassify -g cpu,memory,blkio,devices,freezer:sandbox <PID>
4. cat /sys/fs/cgroup/cpu/sandbox/tasks
5. cgset -r cpu.cfs_period_us=100000 -r cpu.cfs_quota_us=$[ 5000 * $(getconf _NPROCESSORS_ONLN) ] sandbox
6. cgset -r memory.limit_in_bytes=80M sandbox
7. cgget -r memory.stat sandbox
```

**Docker Images**
```
1. docker run --rm -dit --name my-alpine alpine:3.10 sh
2. docker export -o dockercontainer.tar my-alpine
3. docker kill my-alpine
4. mkdir container-root
5. tar xf dockercontainer.tar -C container-root/
6. echo "mount -t proc none /proc
   -mount -t sysfs none /sys
   -mount -t tmpfs none /tmp" >> container-root/mounts.sh
7. unshare --mount --uts --ipc --net --pid --fork --user --map-root-user chroot $PWD/container-root ash 
```

