# Echo Server in Pterodactyl

This Guide will help you set up Echo Servers on Linux with Pterodactyl.

Please use the same paths as I do in this guide. If you dont, stuff might now work as expected.
You also should have a Domain for the Pterodactyl Dashboard to be able to sue SSL/https.
You might be able to do it without, but this Guide will work with a Domain.


I use Debian 12 x86. Other systems should work, but in this Guide I will only show how to get it to run on Debian 12.

To install the basic Pterodactyl instance, please follow the official Guide.
It doesnt make sense for me to just repeat the guide here.
I will provide some additional imformations for some of the steps, if needed. So check below.
```
https://pterodactyl.io/panel/1.0/getting_started.html
```

Before you start you should update your system
```
apt update && apt upgrade -y
```

# Install Docker:
```
https://docs.docker.com/engine/install/debian/
```

# Dependencies Install:

On the Dependency Install, you have to install PHP a different way, as the official Guide uses Ubuntu

I will provide the way I installed the dependencies, but **you have to** check the official Guide for the correct versions. Especially for php
```
apt install -y curl gnupg lsb-release apt-transport-https ca-certificates
wget -qO - https://packages.sury.org/php/apt.gpg | sudo gpg --dearmor -o /usr/share/keyrings/sury-php.gpg
echo "deb [signed-by=/usr/share/keyrings/sury-php.gpg] https://packages.sury.org/php bookworm main" | sudo tee /etc/apt/sources.list.d/sury-php.list
apt update
curl -LsS https://r.mariadb.com/downloads/mariadb_repo_setup | sudo bash
apt update
#Check for the right php version on the official Guide!
sudo apt install -y php8.3 php8.3-{common,cli,gd,mysql,mbstring,bcmath,xml,fpm,curl,zip} mariadb-server nginx tar unzip git redis-server bc
curl -sS https://getcomposer.org/installer | sudo php -- --install-dir=/usr/local/bin --filename=composer


```
Now continue on the "Download Files" Section of the official Guide and **come back for the "Webserver Configuration" Section!** 
For the Application URL when setting up "php artisan p:environment:setup", you should use a Domain with https. You dont have to, but you really should. If you only have a IP, use http://IP

# Webserver Configuration

If you dont have a Domain, you cant use SSL. So just follow the official Webserver Configuration Section without doing the steps here.

- Setup your DNS Server and add a subdomain that is pointing to your servers IP.
  As an example: dash.example.com
```
apt install -y certbot
systemctl stop nginx
certbot --certonly
```
- Select 1: Spin up a temporary webserver (standalone)
- Enter your details
```
systemctl start nginx

```

- Now continue with the Webserver Configuration Section of the official Guide. If you start the "Installing Wings" Section, you dont need to install Docker! We did that already.
- For Total Memory, Memory Over-Allocation, Total Disk Space, Disk Over-Allocation I entered 8000/100%. But less or more should work fine.
- For the Allocation, enter your Servers IP-Address and enter Ports: 7793-7803. This will give you 10 possible Server instances.

**Now we are done with the basic Pterodactyl Installation**

# Clone this Repo:
```
cd /opt
git clone https://github.com/BL00DY-C0D3/pterodactyl_Echo_Server.git
cd pterodactyl_Echo_Server
```

# Run the getBinaries.sh to get the Echo Binaries
```
bash getBinaries.sh
```

# Configure Echo configs

- config.json:
```
nano /opt/ready-at-dawn-echo-arena/_local/config.json
```

This is an example config.json. If you dont know what to enter here, contact your Matchmaking Admin!
```
{
    "apiservice_host":  "http://g.example.com:80/api",
    "configservice_host":  "ws://g.example.com:80/config",
    "loginservice_host":  "ws://g.example.com:80/login?discordid=<YOUR DISCORD ID>&password=<YOUR PASSWORD>",
    "matchingservice_host":  "ws://g.example.com:80/matching",
    "serverdb_host":  "ws://g.example.com:80/serverdb?discordid=<YOUR DISCORD ID>&password=<YOUR PASSWORD>&regions=default,<YOUR REGION CODE>",
    "transactionservice_host":  "ws://g.example.com:80/transaction",
    "publisher_lock":  "echovrce"
}
```
Type CTRL+X, then y, then Enter to save

- start-echo.sh:
```
nano ./scripts/start-echo.sh 
```

Change the region variable to match your region


# Open your Pterodactyl dashboard to setup the server

- Go to Nests -> Create New
  - Enter a name
- Click on New Egg
  - Associated Nest = the Nest you just created
  - Name = Enter a name
  - Docker Images = miarshmallow/echovr
    
  (You can build and upload your own Dockerimage with the provided Dockerfile)
  - 


