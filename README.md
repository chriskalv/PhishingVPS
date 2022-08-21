AWS Setup Guidelines
========================

I am using AWS instances to run 

  + a Hak5 Cloud C2 environment and<p>
  + an Apache2 server to host multiple cloned web pages to be used in penetration testing scenarios for phishing attacks.
  
Both instances are reachable by indivual domains (and several subdomains in case of the Apache2 server).

## Setup
The instructions assume that an AWS instance has been set up with a Ubuntu LTS. For any fresh Install, do these things first:
1. Open necessary ports (443, 8080 and 2022 for CloudC2 e.g.) and assign a static IP in your AWS dashboard.
2. Download the SSH key, convert it with puttyGen and save the login setup (in Putty & FileZilla, for example).
3. Update and Upgrade your system. 
```
sudo apt update
sudo do-release-upgrade
sudo apt upgrade
```
4. Download software, which is often needed.
```
sudo apt install mc
sudo apt install unzip
sudo apt-get -y install python3-pip
```

### -> Cloud C2 Server for Hak5 devices
Instructions start [here](https://www.youtube.com/watch?v=TIpx_ENurLY).

### -> Apache2 Server for phishing attacks
1. Install Apache2 on your AWS instance by entering `sudo apt-get install apache2`.
2. Install the Apache2 Rewrite module by entering `sudo a2enmod rewrite`. After that, restart Apache2 with `sudo systemctl restart apache2`.
3. Install [SET](https://github.com/trustedsec/social-engineer-toolkit) and edit `/etc/setoolkit/set.config`: 
```APACHE_SERVER=ON.```
4. Use SET to clone a web page you want to use for phishing purposes. When asked for the external IP, enter your AWS  static IP.
5. Edit file paths of the newly created clone and move it down the ```/var/www/public:html/``` directory.
6. Repeat steps 4. and 5. for every phishing site that you want to create.
7. Edit permissions.
8. Add `.conf` files for previously created sites and make them reachable individually by your subdomains.


<br></br>
