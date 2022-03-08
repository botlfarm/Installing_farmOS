# Installing_farmOS

Installed remotly on a Linode server. Managed through Windows 10 machine with powershell and chrome.
We are handaling the proxy similar to how this website shows us. 
https://www.singularaspect.com/use-nginx-proxy-and-letsencrypt-companion-to-host-multiple-websites/

## Start Linode Server

Im using a shared 1gb ram nano server running Ubuntu 20.4 LTS

## Domain 
Witout a domain the ssl wont work. 

Point your domain to Linode server ip adress. This will vary depending on where your domain is registered. You will need to set your name servers to point to linode servers and add an a record for your domain to point to your linode ip adress. 

## Prerecuasit for server
Login to your server. Replace [your ip adress] with your ip adress from linode
```
ssh root@[your ip adress]
```
update the package installer and your server
```
apt update
apt upgrade
```
install docker-compose and nano.
```
apt install docker-compose
apt install nano
```

maybe I forgot other things I installed from the bengign as it was a while ago, but I thnk that is all that is needed. 

## Proxy
This is sumerized from https://www.singularaspect.com/use-nginx-proxy-and-letsencrypt-companion-to-host-multiple-websites/

Create the proxy network

```
docker network create nginx-proxy
```

create proxy folder
copy docker-compose.yml to folder
Start up nginx containers

```
docker-compose up -d
```

check for running containers

```
docker ps
```

you should see 2 docker containers running the nginx and the ngnix-companion