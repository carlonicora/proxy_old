# Initialise proxy and letsencrypt

## Add your domain names to etc/hosts starting with the api.dev.phlow.com
```
127.0.0.1 api.dev.phlow.com
```

## Add your main domain configutations to proxy/hosts
Copy `default.443.conf.example` to `com.phlow.dev.api.conf` and edit
Important! Comment server which listens to 443 and 301 redirect from 80 to 433

## Create a container
Run `docker-compose up -d --build`

## Create letsencrypt certificate
Run `./letsencrypt.sh -d api.dev.phlow.com -e YOUREMAILHERE`

## Uncomment lines in subdomain configurations

# HTTPS in proxy for a local development

## Install OpenSSL
Visit https://slproweb.com/products/Win32OpenSSL.html

## Create a private key

```shell
openssl genrsa -des3 -out .\certbot\conf\root\rootSSL.key 2048
```

## Create a certificate file
```shell
openssl req -x509 -sha256 -days 1100 -key .\certbot\conf\root\rootSSL.key -out .\certbot\conf\root\rootSSL.crt -config .\certbot\conf\root\rootCertificateConfig.cnf
```

[Optional] You can check certificate data by running
```shell
openssl x509 -text -noout -in .\certbot\conf\root\rootSSL.crt
```

## Create a certificate for each sub domain using the root certificate

### Create folders for keys

```shell
New-Item -Path .\certbot\conf\live\api.dev.phlow.com -ItemType Directory -Force
New-Item -Path .\certbot\conf\live\app.dev.phlow.com -ItemType Directory -Force
New-Item -Path .\certbot\conf\live\auth.dev.phlow.com -ItemType Directory -Force
```


### Create private keys for sub domains

```shell
openssl req -new -sha256 -nodes -out .\certbot\conf\live\app.dev.phlow.com\app.dev.phlow.com.csr -newkey rsa:2048 -keyout .\certbot\conf\live\app.dev.phlow.com\app.dev.phlow.com.key -config .\certbot\conf\root\rootCertificateConfig.cnf
openssl req -new -sha256 -nodes -out .\certbot\conf\live\api.dev.phlow.com\api.dev.phlow.com.csr -newkey rsa:2048 -keyout .\certbot\conf\live\api.dev.phlow.com\api.dev.phlow.com.key -config .\certbot\conf\root\rootCertificateConfig.cnf
openssl req -new -sha256 -nodes -out .\certbot\conf\live\auth.dev.phlow.com\auth.dev.phlow.com.csr -newkey rsa:2048 -keyout .\certbot\conf\live\auth.dev.phlow.com\auth.dev.phlow.com.key -config .\certbot\conf\root\rootCertificateConfig.cnf
```

### Create new certificates for sub domains using the root SSL certificate

Before each run, change url (subdomain) in domainCertificateConfig.cfg 

```shell
Copy-Item ".\certbot\conf\root\domainCertificateConfig.cfg.example" ".\certbot\conf\live\app.dev.phlow.com\domainCertificateConfig.cfg"
((Get-Content -path .\certbot\conf\live\app.dev.phlow.com\domainCertificateConfig.cfg -Raw) -replace 'SUBDOMAINNAME','app.dev.phlow.com') | Set-Content -Path .\certbot\conf\live\app.dev.phlow.com\domainCertificateConfig.cfg
openssl x509 -req -in .\certbot\conf\live\app.dev.phlow.com\app.dev.phlow.com.csr -CA .\certbot\conf\root\rootSSL.crt -CAkey .\certbot\conf\root\rootSSL.key -CAcreateserial -out .\certbot\conf\live\app.dev.phlow.com\app.dev.phlow.com.crt -days 500 -sha256 -extfile .\certbot\conf\live\app.dev.phlow.com\domainCertificateConfig.cfg
Copy-Item ".\certbot\conf\root\domainCertificateConfig.cfg.example" ".\certbot\conf\live\api.dev.phlow.com\domainCertificateConfig.cfg"
((Get-Content -path .\certbot\conf\live\api.dev.phlow.com\domainCertificateConfig.cfg -Raw) -replace 'SUBDOMAINNAME','api.dev.phlow.com') | Set-Content -Path .\certbot\conf\live\app.dev.phlow.com\domainCertificateConfig.cfg
openssl x509 -req -in .\certbot\conf\live\api.dev.phlow.com\api.dev.phlow.com.csr -CA .\certbot\conf\root\rootSSL.crt -CAkey .\certbot\conf\root\rootSSL.key -CAcreateserial -out .\certbot\conf\live\api.dev.phlow.com\api.dev.phlow.com.crt -days 500 -sha256 -extfile .\certbot\conf\live\api.dev.phlow.com\domainCertificateConfig.cfg
Copy-Item ".\certbot\conf\root\domainCertificateConfig.cfg.example" ".\certbot\conf\live\auth.dev.phlow.com\domainCertificateConfig.cfg"
((Get-Content -path .\certbot\conf\live\auth.dev.phlow.com\domainCertificateConfig.cfg -Raw) -replace 'SUBDOMAINNAME','auth.dev.phlow.com') | Set-Content -Path .\certbot\conf\live\app.dev.phlow.com\domainCertificateConfig.cfg
openssl x509 -req -in .\certbot\conf\live\auth.dev.phlow.com\auth.dev.phlow.com.csr -CA .\certbot\conf\root\rootSSL.crt -CAkey .\certbot\conf\root\rootSSL.key -CAcreateserial -out .\certbot\conf\live\auth.dev.phlow.com\auth.dev.phlow.com.crt -days 500 -sha256 -extfile .\certbot\conf\live\api.dev.phlow.com\domainCertificateConfig.cfg
```

## Get Windows to Trust the Certificate Authority (CA)

### Import certificates
An example of importing certificates into Windows. STEP 3 ONLY from [this instruction](https://zeropointdevelopment.com/how-to-get-https-working-in-windows-10-localhost-dev-environment), import CRT file (no pem file).
1. Windows. Import rootSSL.crt into 'Trusted Root Certification Authorities store' (Доверенные корневые центры сертификации) 
2. Browser. Import rootSSL.crt into 'Trusted Root Certification Authorities store' (Доверенные корневые центры сертификации)
3. Browser. Import all subdomains certificates


## Clean browser cache

## Restart proxy docker container
```shell
docker-compose restart
```