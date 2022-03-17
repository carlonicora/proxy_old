# HTTPS in proxy for a local development

# Install OpenSSL
Visit https://slproweb.com/products/Win32OpenSSL.html

# Create a private key

```shell
openssl genrsa -des3 -out rootSSL.key 2048
```

# Create a certificate file
```shell
openssl req -x509 -sha256 -days 1100 -key rootSSL.key -out rootSSL.crt -config rootCertificateConfig.cnf
```

[Optional] You can check certificate data by running
```shell
openssl x509 -text -noout -in .\rootSSL.crt
```

# Create a certificate for each sub domain using the root certificate

## Create private keys for sub domains

```shell
openssl req -new -sha256 -nodes -out app.dev.phlow.com.csr -newkey rsa:2048 -keyout app.dev.phlow.com.key -config rootCertificateConfig.cnf
openssl req -new -sha256 -nodes -out api.dev.phlow.com.csr -newkey rsa:2048 -keyout api.dev.phlow.com.key -config rootCertificateConfig.cnf
openssl req -new -sha256 -nodes -out auth.dev.phlow.com.csr -newkey rsa:2048 -keyout auth.dev.phlow.com.key -config rootCertificateConfig.cnf
```

## Create new certificates for sub domains using the root SSL certificate

Before each run, change url (subdomain) in domainCertificateConfig.cfg 

```shell
openssl x509 -req -in app.dev.phlow.com.csr -CA rootSSL.crt -CAkey rootSSL.key -CAcreateserial -out app.dev.phlow.com.crt -days 500 -sha256 -extfile domainCertificateConfig.cfg
openssl x509 -req -in api.dev.phlow.com.csr -CA rootSSL.crt -CAkey rootSSL.key -CAcreateserial -out api.dev.phlow.com.crt -days 500 -sha256 -extfile domainCertificateConfig.cfg
openssl x509 -req -in auth.dev.phlow.com.csr -CA rootSSL.crt -CAkey rootSSL.key -CAcreateserial -out auth.dev.phlow.com.crt -days 500 -sha256 -extfile domainCertificateConfig.cfg
```

# Get Windows to Trust the Certificate Authority (CA)

## Import certificates
An example of importing certificates into Windows. STEP 3 ONLY from [this instruction](https://zeropointdevelopment.com/how-to-get-https-working-in-windows-10-localhost-dev-environment), import CRT file (no pem file).
1. Windows. Import rootSSL.crt into 'Trusted Root Certification Authorities store' (Доверенные корневые центры сертификации) 
2. Browser. Import rootSSL.crt into 'Trusted Root Certification Authorities store' (Доверенные корневые центры сертификации)
3. Browser. Import all subdomains certificates

## Clean browser cache