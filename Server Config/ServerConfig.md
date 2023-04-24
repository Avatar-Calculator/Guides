# Steps to configure a new Digital Ocean droplet with Avatar-Calculator repositories
Note: See if there are any prior snapshots. If so, you can restore that droplet for the configurations.

## Install packages
* Refresh local package index: ```sudo apt update```
* Reboot system: ```sudo reboot```
* Install Git: ```sudo apt-get install git```
* Verify Git installation: ```git --version```
* Install Node.js: ```sudo apt-get install nodejs```
* Verify Node.js installation: ```node -v```
* Install npm: ```sudo apt install npm```
* Verify npm installation: ```npm -v```
* Install redis: ```sudo apt install redis-server```
* Verify redis installation: ```redis-cli --version```
    * NOTE: The default configuration file is: ```sudo nano /etc/redis/redis.conf```

## Install Go
* Move to Home Directory: ```cd ~```
* Download Go: ```curl -OL https://go.dev/dl/go1.20.3.linux-amd64.tar.gz```
    * Get the latest release from: https://go.dev/dl/
* Verify Go integrity: ```sha256sum go1.20.3.linux-amd64.tar.gz```
* Extract tarball: ```sudo tar -C /usr/local -xvf go1.20.3.linux-amd64.tar.gz```
* Delete tarball: ```rm go1.20.3.linux-amd64.tar.gz```
* Setting Go Paths: ```sudo nano ~/.profile```
    * ```export PATH=$PATH:/usr/local/go/bin```
* Refresh profile: ```source ~/.profile```
* Verify go installation: ```go version```

## Install repositories
Note: A list of repositories can be found here: https://github.com/orgs/Avatar-Calculator/repositories
* ```cd ~```
* ```mkdir apps```
* ```cd apps/```
* Clone backend: ```git clone https://github.com/Avatar-Calculator/backend.git```
* Clone frontend: ```git clone https://github.com/Avatar-Calculator/frontend.git```
* Clone go-scraper: ```git clone https://github.com/Avatar-Calculator/go-scraper.git```
    * NOTE: Because this is a private repository, you need to enter your GitHub username and personal access token. Only fine-grained tokens are alowed.

## Installing PM2
PM2 is a production process manager
* Install PM2: ```npm i -g pm2```
* Install PM2 Log Rotation: ```pm2 install pm2-logrotate```
* Set Max Size Per Log: ```pm2 set pm2-logrotate:max_size 15M```
    * NOTE: Check PM2 log sizes with ```du -h ~/.pm2/pm2.log```
* Set Amount of Logs to Retain: ```pm2 set pm2-logrotate:retain 10```
* Compress Logs: ```pm2 set pm2-logrotate:compress true```

## Setting up frontend repository
* ```cd ~/apps/frontend```
* Install dependencies: ```npm i```
* Setup env variables: ```sudo nano .env.local```
    * ```NODE_ENV=production```
* Build project: ```npm run build```
* Start project: ```pm2 start npm --name "frontend" -- start```

## Setting up backend repository
* ```cd ~/apps/backend```
* Install dependencies: ```npm i```
* Setup env variables: ```sudo nano .env```
    * ```NODE_ENV=production```
    * ```APP_AES_SECRET={SECRET_GOES_HERE}```
    * ```WEB3_HTTP_PROVIDER={SECRET_GOES_HERE}```
    * ```MONGO_URI={SECRET_GOES_HERE}```
    * ```GOOGLE_APPLICATION_CREDENTIALS="/root/apps/backend/src/config/firebaseKey.json"```
        * NOTE: Add the firebase private key to the above directory.
* Build project: ```npm run build```
* Start project: ```pm2 start npm --name "backend" -- start```

## Seting up go-scraper repository
* ```cd ~/apps/go-scraper```
* Setup env variables: ```sudo nano .env```
    * ```DATABASE=prod```
    * ```MONGO_URI={SECRET_GOES_HERE}```
    * ```PROXY=http://{URL_GOES_HERE}:{PORT_GOES_HERE}```
* ```cd ./src```
* Install dependencies/Create binary: ```go build```
* Give execution permissions: ```sudo chmod +x src```
* ```cd ../```
* Start project: ```pm2 start ./src/src --name "go-scraper"```

## Setting up data-visualization repository
COMING SOON

## Setup NGINX
* Install NGINX: ```sudo apt-get install nginx```
* Edit configuration file: ```sudo nano /etc/nginx/sites-available/default```
    * NOTE: The configuration used is under the "nginx" file.
* Test configuration files: ```nginx -t```
* Restart nginx: ```systemctl restart nginx```

## Setting up basic firewall
* Enable OpenSSH connections: ```sudo ufw allow OpenSSH```
* Enable HTTP/HTTPS traffic: ```sudo ufw allow 'Nginx Full```
* Turn the firewall on: ```sudo ufw enable```
* Verify firewall status: ```sudo ufw status```
    * NOTE: Should only have 'OpenSSH' and 'Nginx Full'.

## Setup LetsEncrypt
* Update snapd core: ```sudo snap install core; sudo snap refresh core```
* Install certbot: ```sudo snap install --classic certbot```
* Link certbot command: ```sudo ln -s /snap/bin/certbot /usr/bin/certbot```
* Verify the app points to avatarcalculator: ```dig +short avatarcalculator.com```
    * NOTE: If this doesn't point to the droplet, point the domains to the droplet on DigitalOcean.
* Obtaining SSL certificate: ```sudo certbot --nginx -d avatarcalculator.com -d www.avatarcalculator.com```
* Verify Certbot Auto-renewal: ```sudo systemctl status snap.certbot.renew.service```

# Steps to configure an existing Digital Ocean droplet after a restart
Note: If the space on the Droplet's disk is full, ```du -h --max-depth=1``` will show which folders are using the most space.

COMING SOON