### Docker Tutorial

##### Setting up root folder:
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