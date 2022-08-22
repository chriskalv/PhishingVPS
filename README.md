Virtual Private Server Setup Guidelines
========================

I am using AWS instances to run 

  + a Hak5 Cloud C2 environment and<p>
  + an Apache2 server to host multiple cloned web pages to be used in penetration testing scenarios for phishing attacks.
  
However, any other VPS should be fine as well. Both AWS instances are reachable by indivual domains (and several instances of domain subfolders in case of the Apache2 server).

## Setup
The instructions assume that a VPS instance has been set up with Ubuntu LTS. For any fresh Install, do these things first:
  1. Open necessary ports (443, 8080 and 2022 for CloudC2 e.g.) and assign a static IP in your VPS dashboard.
  2. Apply a static IP to your VPS instance.
  3. Download the SSH key for the server, convert it (if necessary) with puttyGen and save quickconnect logins for your software (like Putty & FileZilla, for example).
  4. Update and Upgrade your system:
```
sudo apt update
sudo do-release-upgrade
sudo apt upgrade
```
4. Download and install often needed software:
```
sudo apt install mc -y
sudo apt install unzip
sudo apt install python3 python3-pip -y
```

### -> Cloud C2 Server for remote management of Hak5 devices
Instructions start [here](https://www.youtube.com/watch?v=TIpx_ENurLY).

### -> Apache2 Server for phishing attacks
1. Install *Apache2* and *Let's Encrypt* (free SSL certificates via Certbot) on your VPS instance:
```
sudo apt-get install apache2
sudo apt install python3-venv libaugeas0 -y
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
sudo /opt/certbot/bin/pip install certbot certbot-apache
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
```
2. Install the Apache2 Rewrite and php module. Then restart Apache2:
```
sudo a2enmod rewrite
sudo apt-get install libapache2-mod-php5
sudo systemctl restart apache2
```
3. Install [SET](https://github.com/trustedsec/social-engineer-toolkit) and edit `/etc/setoolkit/set.config`:
```APACHE_SERVER=ON.```
4. Use SET (`sudo setoolkit`) to clone a web page that you would like to use for phishing purposes. When asked for the external IP, enter the static IP of your VPS.
5. Rename `/var/www/html` to something like `/var/www/<nameofyourclonedwebsite>/` and create a new `/html` folder.
6. Repeat steps 4. and 5. for every phishing site that you want to create.
7. Edit permissions of the entire website directory by entering `sudo chmod -R 755 /var/www`.
8. Create a `.conf` file in `/etc/apache2/sites-available/` and paste + edit the following lines to your needs:
```html
<VirtualHost *:80>
    ServerName yourdomainhere.com
    ServerAlias www.yourdomainhere.com
    DocumentRoot /var/www/landingpage/

    Alias /facebook /var/www/facebook
    Alias /instagram /var/www/instagram
    Alias /linkedin /var/www/linkedin

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
9. Enable/disable virtual hosts:
    - Enable the newly created virtual host with `sudo a2ensite <yourconfig.conf>`.
    - Disable the default Apache2 website by entering `sudo a2dissite 000-default.conf`.
    - Restart Apache2 with `sudo systemctl restart apache2`
    
UNSURE:

10. Create a SSL certificate for your new virtual host with help of *Let's Encrypt* by entering `sudo certbot --apache -d yourdomainhere.com -d www.yourdomainhere.com`.


<br></br>
