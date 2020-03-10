# How to install and use the nginx proxy + letsencrypt

To use the nginx proxy to serve mulitple instances of dockerised nginx in the same servers you need to do the following:

## duplicate the configuration example

In proxy/hosts copy the file `default.conf.example` and rename is so it fits your endpoint. Please make sure to use the extension `.conf`

Edit your new file and make sure to replace the following:

`{HOSTNAME}` with the url of your endpoint

`{DOCKERNAME}` with the name of the nginx instance of your docker exposing port 80

## Install and run this docker

```
cd .docker
docker-compose build
docker-compose up -d
```

## Open port 80 and 443 in UFW

```
sudo ufw allow 80
sudo ufw allow 443
```

## Make sure you can run letsencrypt.sh

```
sudo chmod +X letsencrypt.sh
```

## Test the creation of the certificate

```
sudo ./letsencrypt.sh -d YOURDOMAINNAMEHERE -e YOUREMAILHERE -s
```

## Create the certificate

```
sudo ./letsencrypt.sh -d YOURDOMAINNAMEHERE -e YOUREMAILHERE
```

## Edit your configuration

In your configuration file in `proxy/hosts` you need to remove all the comments

## Restart the docker

```
cd .docker
docker-compose down
docker-compose up -d
```

You are good to go, the proxy will manage the certificate, and your nginx should only expose port 80
