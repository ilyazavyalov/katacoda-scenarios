Description: https://www.katacoda.com/courses/docker-security/sysdig-falco

Step 1 - Sysdig Falco installation

$ sudo -s
$ mkdir /etc/falco
$ cd /etc/falco
$ curl https://raw.githubusercontent.com/katacoda-scenarios/sysdig-scenarios/master/sysdig-falco/assets/falco.yaml -o falco.yaml
$ curl https://raw.githubusercontent.com/katacoda-scenarios/sysdig-scenarios/master/sysdig-falco/assets/falco_rules.yaml -o falco_rules.yaml
$ touch /var/log/falco_events.log

$ docker pull sysdig/falco
$ docker run -d --name falco --privileged -v /var/run/docker.sock:/host/var/run/docker.sock -v /dev:/host/dev -v /proc:/host/proc:ro -v /boot:/host/boot:ro -v /lib/modules:/host/lib/modules:ro -v /usr:/host/usr:ro -v /etc/falco/falco.yaml:/etc/falco/falco.yaml -v /etc/falco/falco_rules.yaml:/etc/falco/falco_rules.yaml -v /var/log/falco_events.log:/var/log/falco_events.log sysdig/falco

Step 2 - Container running interactive shell(detecting an attacker running an interactive shell in any of your containers)

$ docker run -d -P --name example1 nginx
$ tail /var/log/falco_events.log (see 17:13:24.357351845: Notice A shell was spawned in a container with an attached terminal (user=root example1 (id=604aa46610dd) shell=bash parent=<NA> cmdline=bash terminal=34816)
)
Step 3 - Unauthorized process

$ cd /etc/falco
$ curl https://raw.githubusercontent.com/katacoda-scenarios/sysdig-scenarios/master/sysdig-falco/assets/falco_rules_step3.yaml -o falco_rules.yaml
$ docker run -d -P --name example2 nginx
$ docker exec -it example2 ls
$ tail /var/log/falco_events.log (out: 18:38:43.364877988: Warning Unauthorized process (ls ) running in (604aa46610dd))

Step 4 - Write to non user-data directory

$ sudo -s
$ cd /etc/falco
$ curl https://raw.githubusercontent.com/katacoda-scenarios/sysdig-scenarios/master/sysdig-falco/assets/falco_rules_step4.yaml -o falco_rules.yaml
$ docker restart falco
$ docker run -d -P --name example3 nginx
$ docker exec -it example3 bash
root@df2a13847c56:/# mkdir /userdata
root@df2a13847c56:/# touch /userdata/foo # Shouldn't trigger this rule
root@df2a13847c56:/# touch /usr/foo # But this will do
root@df2a13847c56:/# exit

20:39:17.538716395: Error Writing to non user_data dir (user=root command=touch /usr/foo file=/usr/foo)

Step 5 - Sensitive mount by container
see above
This is the rule that watches for sensitive mounts inside containers:

- rule: Launch Sensitive Mount Container
  desc: >
    Detect the initial process started by a container that has a mount from a sensitive host directory
    (i.e. /proc). Exceptions are made for known trusted images.
  condition: evt.type=execve and proc.vpid=1 and container and sensitive_mount and not trusted_containers
  output: Container with sensitive mount started (user=%user.name command=%proc.cmdline %container.info)
  priority: INFO
  tags: [container, cis]
  
  Step 6 - Falco event generator
$ docker pull sysdig/falco-event-generator
$ docker run -d --name falco-event-generator sysdig/falco-event-generator
