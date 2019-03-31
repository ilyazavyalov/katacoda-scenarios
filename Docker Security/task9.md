Description:ssh-keys

Step 1 0f 3 Start OnVault

$ docker run -it -v ~/.ssh:/vault/.ssh ubuntu /bin/bash -c "echo mysupersecret > /vault/.ssh/key"
$ docker run -d -p 172.18.0.1:14242:3000 -v ~/.ssh:/vault/.ssh dockito/vault (build secrets server)

Step 2 - Build Docker Image
$ cat Dockerfile-onvault
$ docker build -f Dockerfile-onvault -t onvault-test

Step 3 - Inspect Image
$ docker run -it onvault-test ls /root/.ssh
