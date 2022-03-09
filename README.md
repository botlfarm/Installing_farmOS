# Installing_farmOS

This is a walkthrough for installing farmOS 2.0.0-beta3 remotely on a Linode server. Managed through Windows 10 machine using PowerShell and chrome.

Before this I had little experience using a command line or starting/using a cloud server. I tried my best to document the process, hopefully I did not miss any steps. 

## Start Linode Server

I'm using a shared 1gb ram nano server running Ubuntu 20.4 LTS. This guide from Linode helps walk through this process. https://www.linode.com/docs/guides/getting-started/

## Domain 
You need to have a domain name pointed to the linode server for ssl to work. 

Point your domain name to your Linode server IP address. This process will vary depending on where your domain name is registered. You will need to set your name servers to point to your Linode servers and add a 'record' for your domain to point to your Linode IP address. Linode has a nice write up to help with this. https://www.linode.com/docs/guides/dns-manager/

## Setup of server
In PowerShell login to your server. Replace [your IP address] with your IP address assigned to you by Linode
```
ssh root@[your IP address]
```
You will need to enter the root password you assigned while creating the Linode server.

**Note:** it won't look like you're typing anything when entering your password, just type it and hit enter.

Update the package installer and your server
```
apt update
apt upgrade
```
Install docker-compose and nano.
```
apt install docker-compose nano
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
curl https://raw.githubusercontent.com/botlfarm/Installing_farmOS/main/proxy/docker-compose.yml -o docker-compose.yml
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
curl https://raw.githubusercontent.com/botlfarm/Installing_farmOS/main/farmOS/docker-compose.yml -o docker-compose.yml
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

Once you get through the next few setup screens you can configure the filesystem and key folder if you need that, if not you are good to start using farmOS

## File system and keys folder permission
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

Start back up farmOS docker containers and check for running containers
```
docker-compose up -d
docker ps
```
To set Apache permissions for the `keys` folder for OAuth2 you need to login to the farmOS docker container. From the above list of docker containers find the farmOS image and note the [container id]
```
docker exec -it [container id] /bin/bash
```
You are now in the farmOS docker container. Add write permissions to the `keys` folder
```
chown -R www-data:www-data keys
chmod -R 440 keys
```
You can now go back to the browser and navigate to `https://yourdomain.com/admin/config/people/simple_oauth`. Select `Generate Keys` at the bottom. For the required directory use `/opt/drupal/keys`. Click `Generate` then `Save Configuration`.

farmOS is now setup and ready to use or ready to migrate from 1.x. 

Hope this helps you get farmOS up and running. 