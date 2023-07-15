# PROJECT 10: Load Balancer Solution With Nginx and SSL/TLS

## CONFIGURE NGINX AS A LOAD BALANCER

### Creating  EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections

### Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

![Alt text](<Images/Screenshot 2023-07-15 100353.png>)


### Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers

`sudo apt update`
`sudo apt install nginx`

![Alt text](<Images/Screenshot 2023-07-14 151225.png>)

![Alt text](<Images/Screenshot 2023-07-14 152737.png>)

![Alt text](<Images/Screenshot 2023-07-14 160722.png>)


### Configure Nginx LB using Web Servers’ names defined in /etc/hosts

`sudo vi /etc/nginx/nginx.conf`

### insert following configuration into http section

 upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#       include /etc/nginx/sites-enabled/*;

![Alt text](<Images/Screenshot 2023-07-14 154729.png>)

![Alt text](<Images/Screenshot 2023-07-14 180852.png>)


### REGISTER A NEW DOMAIN NAME AND CONFIGURE SECURED CONNECTION USING SSL/TLS CERTIFICATES

### Register a new domain name 

### Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP

### Create an elastic IP for the EC2 instance you are integrating.

- Click Allocate new address in the Elastic IPs page.
- Then, click Allocate in the next page.
- Right-click the row of the newly created elastic IP, and click Associate address.
- Choose the EC2 instance you are integrating

### Connect the elastic IP to your domain.

- Go to GoDaddy’s Domain Manager.
- Go to the Manage DNS of the domain you are integrating.
- Replace the Value of record with Type A with the elastic IP you just created.
- Wait for changes to reflect (This takes at least 600 seconds to reflect, depending on the TTL you specified).
- To check if successful, the domain should now load the EC2 instance you pointed it too.

### Check that your Web Servers can be reached from your browser using new domain name using HTTP protocol – http://raamsafety.com

![Alt text](<Images/Screenshot 2023-07-14 180750.png>)

### Configure Nginx to recognize your new domain name

![Alt text](<Images/Screenshot 2023-07-14 181646.png>)

### Install certbot and request for an SSL/TLS certificate

### Make sure snapd service is active and running

`sudo systemctl status snapd`

### Install certbot

`sudo snap install --classic certbot`

![Alt text](<Images/Screenshot 2023-07-14 182114.png>)

### Request your certificate

`sudo ln -s /snap/bin/certbot /usr/bin/certbot`
`sudo certbot --nginx`

![Alt text](<Images/Screenshot 2023-07-14 183030.png>)

![Alt text](<Images/Screenshot 2023-07-14 183251.png>)

`sudo certbot renew --dry-run`

### configure a cronjob to run the command twice a day

`crontab -e`

Add the following lines

* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1

![Alt text](<Images/Screenshot 2023-07-14 184536.png>)

The End


