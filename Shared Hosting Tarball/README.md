# Shared Hosting Tarball

This is a walkthrough for installing farmOS 2.0.0-beta4 in a shared hosting environment with a tarball install. I use a basic hosting plan from HostPapa and cPanel version 100.0 (build 12)

**Note** I have a legacy hostPapa plan grandfathered from lunarpages that allows for PostgreSQL databases. Not all plans allow for this out of the box.

## cPanel PHP Settings

In cPanel home open the `Select PHP version` setting. 

In the Extension tab set the  PHP version is `7.4`

Make sure the `bcmath`,  `geos`, `pdo_pgsql` and `psql` are checked

Select the `Options` tab and change these settings if available (note you may not have all these options available)

```
memory_limit=256M
max_execution_time=240
max_input_time=240
max_input_vars=5000
realpath_cache_size=4096K
realpath_cache_ttl=3600
```

The setting save automatically when you make them. 

## cPanel PostgreSQL Setup
Back in cPanel home select `PostgreSQL Database Wizard`

Follow the on screen prompts to create your database. Keep track of the database name, user name and password you use. 
**Note** your database name and username with have the prefix of your account name with an underscore. For this example I will use the prefix of XXXX_

For this example I will use the following for my database
```
Databsae name: XXXX_farmos2x
Username: XXXX_farm
Password: farm
```

After finishing the prompts you can go back to cPanel home you can verify your database was created by going to `PostgreSQL Databases`. Under the current databases section you should see your new database with the new user added.

## cPanel File Manager
Here we will add the tarball to your website.

Start by downloading the tar.gz file form https://github.com/farmOS/farmOS/releases to your computer.

From cPanel home click on `File Manager` and navigate to your public_html folder.

At the top click `upload` and upload the tar.gz file you previously downloaded. 

Select the file and at the top select `extract`. the folder should auto populate to `public_html`. this is good. 

This created a new folder called `farmOS`. For clarity and helping with upgrades/backup rename this folder to something like `farmOS2x`. 


## Subdomain
For this example I created a subdomain to host farmOS.

From cPanel home click on `Subdomains`. 

Name your subdomain `farmos2x` (or whatever you want) click in the `document root` section and change this to `/public_html/farmOS2x/web`. 

Press `Submit` to finish setup. 

## Set up farmOS
In a web browser navigate to your subdomain. 

database select prosgreSQL
enter your info 