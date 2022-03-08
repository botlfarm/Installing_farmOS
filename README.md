# Installing_farmOS

This is a walkthrough for installing farmOS 2.0.0-beta3 remotely on a Linode server. Managed through Windows 10 machine using PowerShell and chrome.

Before this I had little experience using a command line or starting/using a cloud server. I tried my best to document the process, hopefully I did not miss any steps. 

## Start Linode Server

I'm using a shared 1gb ram nano server running Ubuntu 20.4 LTS. This guide from Linode helps walk through this process. https://www.linode.com/docs/guides/getting-started/

## Domain 
You need to have a domain name pointed to the linode server for ssl to work. 

Point your domain to Linode server IP address. This will vary depending on where your domain is registered. You will need to set your name servers to point to linode servers and add an 'a record' for your domain to point to your linode IP address. Linode has a nice write up to help with this. https://www.linode.com/docs/guides/dns-manager/

## Setup of server
In PowerShell login to your server. Replace [your IP address] with your IP address assigned to you by Linode
```
ssh root@[your IP address]
```
You will need to enter the root password you assigned while creating the Linode server. Note:it won't look like your typing anything when entering your password, just type it and hit enter

Update the package installer and your server
```
apt update
apt upgrade
```
Install docker-compose and nano.
```
apt install docker-compose
apt install nano
```

## Proxy
This is summarized from https://www.singularaspect.com/use-nginx-proxy-and-letsencrypt-companion-to-host-multiple-websites/

Create the proxy network
```
docker network create nginx-proxy
```
Create proxy folder, copy the docker-compose.yml from github
```
mkdir proxy
cd proxy
curl https://raw.githubusercontent.com/botlfarm/Installing_farmOS/main/proxy/docker-compose.yml -o docker-dompose.yml
```
Open the yml file in nano, change the email address to your email
```
nano docker-compose.yml
```
After changing your email address exit nano (ctrl+X, Y, enter).

Start up nginx containers
```
docker-compose up -d
```
Check for running containers
```
docker ps
```
You should see 2 docker containers running the nginx and the nginx-companion

## farmOS
Create farmOS folder, copy the docker-compose.yml file form github
```
cd ..
mkdir farmOS
cd farmOS
curl https://raw.githubusercontent.com/botlfarm/Installing_farmOS/main/farmOS/docker-compose.yml -o docker-dompose.yml
```
Open the yml file in nano, change the username and password on the database settings (you will need these later when in the browser) and replace example.com with your domain name. 
```
nano docker-compose.yml
```
After you make the changes exit nano (ctrl+X, Y, enter)

Start up the farmOS docker containers
```
docker-compose up -d
```
check to make sure the containers are running
```
docker ps
```
You should now see the two proxy containers and two farmOS container. One for farmOS and one for the database.

You should be able to finish farmOS setup by going to your domain in a browser. 
Change the database type to `PostgreSQL`, use the login info from your yml file, the database name is `farm`, under advanced the host should be `db`.

Once you get through the next few setup screens you can configure the filesystem if you need that, if not you are good to start using farmOS

## File system
Shut down the docker container and open the settings.php file in nano
```
docker-compose down
nano sites/default/settings.php
```
Add to the end of the file (paste with right click)
```
$settings['file_private_path'] = '/opt/drupal/web/sites/default/private/files';
```
Exit nano (ctrl+X, Y, enter)

Start back up farmOS docker containers
```
docker-compose up -d
```
You can now go back to the browser and use farmOS.

Hope this helps you get farmOS up and running. 