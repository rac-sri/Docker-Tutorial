# Docker Tutorial

### _Below Code is Never gonna be used_

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

**Alternatively:** _Plus add Namespaces_

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

---

### _BELOW CODE IS GONNA GET USED_

```
1. docker run --interactive --tty <name>:<version>
   - docker run -it --name <give name to the process> <name>:<version>
2. docker run -it --detach ubunto:bionic //run in background
3. docker ps // get running processes
4. docker attach <name>

* docker image prune // clear installed images to save space
* docker kill <name|hash>
* docker logs <docker name>
  - docker rm <docker name> //since docker kills it around after kill to , so to remove it from there either add -rm to run command or follow this.
* sudo docker run -it node:12-stretch //get node container
  - sudo docker run -it node:12-stretch bash // enter bash instead of node env
```

**DOCKER CLI**

```
Some fun stuff :
1. docker pull jturpin/hollywood
```

**Important Commands**

```
1. docker inspect node:12-stretch
2. docker -dit jturpin/hollywood //to detach and run in background
3. docker pause <container id from docker ps>
4. docker unpause
5. docker kill <container id>
6. docker history <docker name>
7. docker container prune
8. docker image list
9. docker restart <container name>
10. docker search <name>
11. docker exec -it <container_id_or_name> echo "I'm inside the container!"
12. docker system prune -af --volumes  // Remove all unused containers, networks, images (both dangling and unreferenced), and optionally, volumes.
```

**Run a docker file**

```
1. docker build --tag <name> <path>
   - docker build --tag <name> -f <filename> <path>
2. docker run <container id | name if specified>
3. docker run --init --rm --publish 3000:3000 nodeapp //for sigterm shortcut using init , and take port 3000 and expose it on host port 3000
4. EXPOSE 3000 //in Dockerfile
   - docker run --init --rm -P nodeapp  and use the port you get from docker ps.  //not used genrally because of this
5. docker inspect <name>
6. docker run --mount type=bind,source="$(pwd)"/build,target=/usr/share/nginx/html -p 8080:80 nginx
# if you want to make bind mounts (this creates a portal to the host comp. Any changes by host are reflected in dock and vice versa)
7. docker run --env DATA_PATH=/data/num.txt --mount type=volume,src=incrementor-data,target=/data incrementor //volume bind (preferred)
```

**Docker Network**

```
1. docker network ls
// connect mongo eg
2. docker network create --driver=bridge <name her app-net> // create network
3. docker run -d --network=app-net -p 27017:27017 --name=db --rm mongo:3 // to run mongo server
4. docker run -it --network=app.net --rm mongo:3 mongo --host db // to run mongo client
```

**Docker Compose**

```
1. docker-compose up
   - docker-compose up --build //force to rebuild if the dockerfile got modifies
2. docker-compose up --scale web=10 //start 10 web containers if multiple sever interaction needs to be observed
```

**KCOMPOSE**

```
1. install KCOMPOSE,minikube.
2. minikube start  // to install kubernetics image and start the service
3. kompose proxy --port 8080 & // & to run it in background
4. kcompose up
5. kubectl scale --replicas=5 depooyment/web  // 5 web containers run, used to scale apps using load **load balancer**
6. kubectl get all
7. kubectl delete all --all
8. kompose convert // get kubernetic configuration using kompose
```

#### Resources

[Course Content](https://btholt.github.io/complete-intro-to-containers)

[Working with Google Cloud Kubernetes Engine](https://cloud.google.com/kubernetes-engine/docs/quickstart)
