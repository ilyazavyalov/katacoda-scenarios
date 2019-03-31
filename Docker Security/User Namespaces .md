Description: 
In this scenario you'll learn how to configure User Namespaces to add additional user isolation and remap container root users to non-privileged users on the host machine.

Step 1 of 4
 ps aux | grep docker -  list of process 
 docker run --rm alpine id
 sudo cp /bin/touch /bin/touch.bak && ls -lha /bin/touch.bak - copy of the touch command 
 docker run -it -v /bin/:/host/ alpine rm -f /host/touch.bak -the container is capable of deleting the touch binary from the host.
 
 Step 2 
 docker run --user=1000:1000 --rm alpine id
 docker run --user=1000:1000 -it -v /bin:/host/ alpine rm -f /host/touch.bak (premission denied)
 
 Step 3
 curl https://gist.githubusercontent.com/BenHall/bb878c99d06a63cd8ed4d1c0a6941df4/raw/76136ffbca341846619086cfe40ab8e013683f47/daemon.json -o /etc/docker/daemon.json && sudo service docker restart
 cat /etc/docker/daemon.json
 docker info | grep "Root Dir" 
 Step 4 - User Namespaces Protection
 
 By using User Namespaces, it's possible to separate Docker root users and provide more security and isolation than previously available.
 
 
