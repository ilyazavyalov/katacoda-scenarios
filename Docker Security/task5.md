Description:  this scenario you will learn how to block containers gaining additional user privileges, even when running as a non-root user.
 
 Step 1 of 3 
[root@host01 ~]# echo 'FROM benhall/strace-ubuntu:latest' >> 1_Dockerfile
[root@host01 ~]# echo 'ADD 1_testnnp /testnnp' >> 1_Dockerfile
[root@host01 ~]# echo 'RUN chmod u+s /testnnp' >> 1_Dockerfile
[root@host01 ~]# echo 'CMD ["/testnnp"]' >> 1_Dockerfile - Create dockerfile.

curl -LO https://github.com/katacoda/oscon2016-docker-perf-sec/raw/master/tutorial/2_Security/3_no-new-privileges/1_testnnp && chmod +x 1_testnnp - download
docker build -f 1_Dockerfile -t new-priv-1 -build
docker run -u 1000 new-priv-1 (output:Effective uid: 0)

Step 2 -same

Finally:use flag --security-opt=no-new-privileges
