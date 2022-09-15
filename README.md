Phishing VPS Setup
========================
These are some personal notes for my VPS instances, just in case I have to trace back errors. They might come in handy for you if you would like to set up a remote DNSspoof attack via a self-hosted Hak5 Cloud C2 environment and some self-hosted phishing sites as well.

:x: **I assume ZERO responsibility for what you do with this. My intention is to demo potential vulnerabilities.** :x:

Situation:
I am using AWS VPS instances to run 

  + a Hak5 Cloud C2 environment and<p>
  + an Apache server to host multiple cloned web pages to be used in penetration testing scenarios for phishing attacks.
  
However, any other VPS should be fine as well. Both of my AWS instances are reachable by indivual domains (and several instances of domain subfolders in case of the Apache server).

## Setup
The instructions assume that a VPS instance has been set up with Ubuntu LTS. For any fresh Install, do these things first:
  1. Open necessary ports (Port 443, 8080 and 2022 for CloudC2 / Port 80 and 443 for Apache).
  2. Assign a static IP to your VPS instance.
  3. Download the SSH key for the server, convert it (if necessary) with puttyGen and save quick logins for the applications you use (Putty & WinSCP or FileZilla, for example).
  4. Update and Upgrade your system:
```
sudo apt update
sudo do-release-upgrade
sudo apt upgrade
```
4. Download and install software you'll need in any case:
```
sudo apt install mc -y
sudo apt install unzip
sudo apt-get -y install python3-pip
```
### -> Cloud C2 Server for remote management of Hak5 devices
Instructions start [here](https://www.youtube.com/watch?v=TIpx_ENurLY).

### -> Apache Server for phishing attacks
In order for a html server to work, ports 80 (HTML) and 443 (HTTPS) need to be open. Please make sure to do that beforehand by adjusting your `ufw` settings on your linux environement and/or those of your VPS host directly.
1. Install Apache with `sudo apt-get install apache2`
2. Install *Rewrite* & *PHP5* modules for Apache:
```
sudo a2enmod rewrite
sudo apt-get install libapache2-mod-php5
sudo systemctl restart apache2
```
3. Install [SET](https://github.com/trustedsec/social-engineer-toolkit) and edit `/etc/setoolkit/set.config`:
```APACHE_SERVER=ON.```
4. Use SET (`sudo setoolkit`) to clone a web page that you would like to use for phishing purposes. When asked for the external IP, enter the static IP of your VPS followed by a forward slash and the subfolder you intend to use for this website, like `11.112.13.14/facebook`. The cloned web page will be saved to `/var/www/html`.
5. Rename `/var/www/html` to something like `/var/www/<nameofyourclonedwebsite>/` and create a new `/html` folder.
6. Open the `index.html` file of your cloned website and look for a link to the `post.php` reference in there. Adjust the path of said reference to also resemble your domain structure (`http://yourdomain.com/<nameofyourclonedwebsite>/post.php` instead of `http://0.0.0.0/post.php`)
6. Repeat steps 4. and 5. for every phishing site that you want to create.
7. Edit permissions of the entire website directory by entering `sudo chmod -R 755 /var/www`.
8. Create a `yourdomain.com.conf` file in `/etc/apache2/sites-available/`, paste the following lines and edit them according to your needs:
```html
<VirtualHost *:80>
    ServerName yourdomain.com
    ServerAlias www.yourdomain.com
    DocumentRoot /var/www/yourdomain.com/

    Alias /facebook /var/www/facebook.yourdomain.com
    Alias /instagram /var/www/instagram.yourdomain.com
    Alias /linkedin /var/www/linkedin.yourdomain.com

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
9. Enable/disable virtual hosts:
    - Enable the newly created virtual host with `sudo a2ensite <yourdomain.com.conf>`.
    - Disable the default Apache2 website by entering `sudo a2dissite 000-default.conf`.
    - Restart Apache2 with `sudo systemctl restart apache2`

The phishing pages should already work now. You can test this by heading to `yourdomain.com/phishingpageXYZ`. However, they do not have an SSL certificate, yet, which most browsers will highlight as a potential risk for the user. Hence, we will acquire and apply one by help of [these instructions](https://www.digitalocean.com/community/tutorials/how-to-secure-apache-with-let-s-encrypt-on-ubuntu-20-04).

10. Install *Let's Encrypt* (free SSL certificates via Certbot) on your VPS instance and create SSL certificates for all selected pages.
```
sudo apt install certbot python3-certbot-apache
sudo certbot --apache
```

<br></br>
