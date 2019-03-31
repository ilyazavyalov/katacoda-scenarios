Description: In this scenario, we'll explore how to store secrets in Hashicorp Vault. The scenario explains how to initialise a vault, store key/values in a secure way that can later be accessed via the CLI or the HTTP API. The HTTP API is an excellent way to obtain secrets when running inside a Docker Container.

What is Hashicorp Vault
Vault is a tool for securely accessing secrets. A secret is anything that you want to tightly control access to, such as API keys, passwords, certificates, and more. Vault provides a unified interface to any secret, while providing tight access control and recording a detailed audit log. More details can be found at https://github.com/hashicorp/vault/

Step 1 of 8

$ cat vault.hcl - config
$ docker create -v /config --name config busybox; docker cp vault.hcl config:/config/ (Create Data Container)

Step 2 - Launch
$ docker run -d --name consul \
     -p 8500:8500 \
    consul:v0.6.4 \
    agent -dev -client=0.0.0.0
$ cgswong/vault:0.5.3 server -config=/config/vault.hcl

Step 3 - Initialise
Configure Environment

$ alias vault='docker exec -it vault-dev vault "$@"'
$ export VAULT_ADDR=http://127.0.0.1:8200
Initialise Vault
$ vault init -address=${VAULT_ADDR} > keys.txt

Step 4 - Unseal Vault
$ vault unseal -address=${VAULT_ADDR} $(grep 'Key 1:' keys.txt | awk '{print $NF}')
$ vault unseal -address=${VAULT_ADDR} $(grep 'Key 2:' keys.txt | awk '{print $NF}')
$ vault unseal -address=${VAULT_ADDR} $(grep 'Key 3:' keys.txt | awk '{print $NF}')
$ vault status -address=${VAULT_ADDR}

Step 5 - Vault Tokens
$ export VAULT_TOKEN=$(grep 'Initial Root Token:' keys.txt | awk '{print substr($NF, 1, length($NF)-1)}')
$ vault auth -address=${VAULT_ADDR} ${VAULT_TOKEN}

Step 6 - Read/Write Data
Save Data
$ vault write -address=${VAULT_ADDR} \
>   secret/api-key value=12345678
Read Data
$ vault read -address=${VAULT_ADDR} \
  secret/api-key
  
Step 7 - HTTP API
$ curl -H "X-Vault-Token:$VAULT_TOKEN" \
  -XGET http://docker:8200/v1/secret/api-key 
$ $ curl -s -H  "X-Vault-Token:$VAULT_TOKEN" \
>   -XGET http://docker:8200/v1/secret/api-key \
>   | jq -r .data.value


  



