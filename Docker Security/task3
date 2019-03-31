Description: Clair is an Open Source project from CoreOS, designed to scan Docker Images for Security Vulnerabilities.

In this scenario you will learn how to deploy Clair and scan Docker Images from the Docker Hub and private registries for known Vulnerabilities.

Step 1 of 6
  
 curl -OL https://raw.githubusercontent.com/coreos/clair/master/contrib/compose/docker-compose.yml 
 mkdir clair_config && curl -L https://raw.githubusercontent.com/coreos/clair/master/config.yaml.sample -o clair_config/config.yaml
 sed 's/clair-git:latest/clair:v2.0.1/' -i docker-compose.yml && \
 sed 's/host=localhost/host=postgres password=password/' -i clair_config/config.yaml(update config)
 docker-compose up -d postgres(start DB)
 
 Step 2 - Populate DB
 docker run -it \
>     -v $(pwd):/sql/ \
>     --network "${USER}_default" \
>     --link clair_postgres:clair_postgres \
>     postgres:latest \
>         bash -c "PGPASSWORD=password psql -h clair_postgres -U postgres < /sql/clair.sql"

Step 3 - Deploy Clair

docker-compose up -d clair

Step 4 - Scan Image
  1) - curl -L https://github.com/optiopay/klar/releases/download/v1.5/klar-1.5-linux-amd64 -o /usr/local/bin/klar && chmod +x $_

  CLAIR_ADDR=http://localhost:6060 CLAIR_OUTPUT=Low CLAIR_THRESHOLD=10 \
  klar quay.io/coreos/clair:v2.0.1 (само сканирование)
  
  вывод: 
  Found 5 vulnerabilities
CVE-2016-9840: [Medium]

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-9840
-----------------------------------------
CVE-2016-9842: [Medium]

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-9842
-----------------------------------------
CVE-2017-15650: [Medium]

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-15650
-----------------------------------------
CVE-2016-9843: [High]

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-9843
-----------------------------------------
CVE-2016-9841: [High]

https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2016-9841
-----------------------------------------
Medium: 3
High: 2

Step 5 - JSON Output (выходные данные в json)
  pacman -Syy jq
 CLAIR_ADDR=http://localhost:6060 CLAIR_OUTPUT=High CLAIR_THRESHOLD=10 JSON_OUTPUT=true klar postgres:latest | jq
 
 
Step 6 - Scan Private Image(тоже самое, но сканирование приватных образов)


