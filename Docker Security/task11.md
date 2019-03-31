Descroption:In this scenario, we'll access secrets stored in Hashicorp Vault from a Docker Container. To access the stored secrets, the container is configured to use a Volume Driver called LibSecret. The Volume Driver communicates with Vault meaning the applications don't require any additional configuration to access the secrets.

The aim is to stop using environment variables for passwords. The use of environment variables can lead to accentually leaking keys to untrusted applications or storage. Instead, you can store them securely in Vault and access them via the file-system when required.

Step 1 of 4

Configure Vault
[root@host01 ~]# ./start-vault.sh
[root@host01 ~]# ./unseal-vault.sh
[root@host01 ~]# export VAULT_TOKEN=$(grep 'Initial Root Token:' keys.txt | awk '{print substr($NF, 1,length($NF)-1)}')
[root@host01 ~]# vault auth -address=${VAULT_ADDR} ${VAULT_TOKEN}

Step 2 - Save data

[root@host01 ~]# vault write -address=${VAULT_ADDR} \
>   secret/app-1/db-password value=mysecretpassword

Step 3 - Configure Docker Volume Driver

Launch Plugin

nohup docker-volume-libsecret \
>   --addr $VAULT_ADDR \
>   --backend vault \
>   --store-opt token=$VAULT_TOKEN </dev/null &> libsecretlogs &

Start Container

 docker run -ti --rm \
>   --volume-driver libsecret \
>   -v secret/app-1/:/secrets \
>   alpine ash
