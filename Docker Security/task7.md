Description:
In this scenario, you will learn how to install and apply AppArmor profiles.
In the second step of the scenario, you will learn how to create and customise your own AppArmor profile by using Bane, a tool created by Jess Frazelle

Step 1 of 2

Upgrade Docker
curl -fsSL get.docker.com | bash
cat docker-nginx
Parse
sudo apparmor_parser -r -W docker-nginx

Run With Profile
docker run --security-opt apparmor=docker-nginx -d --name apparmor-nginx nginx

View Status
aa-status

Test

docker exec -it apparmor-nginx bash - result:root@2f84b7fc8c17:/

Step 2 - Bane

sudo curl -L http://assets.joinscrapbook.com/bane -o /usr/local/bin/bane - install
cat sample.toml - View Sample
sudo bane sample.toml - create profile
docker run -d --security-opt="apparmor:docker-nginx-sample" --name nginx nginx:1.9 - start Container
docker exec -it nginx bash
-touch,sh..


