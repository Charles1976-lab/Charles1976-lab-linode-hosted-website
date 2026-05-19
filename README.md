Personal Website Deployment on Linode with Nginx and Cloudflare DNS
This repository documents the steps used to deploy a personal website to a Linux server hosted on Akamai Cloud (formerly Linode), configure Nginx, and connect the server to a custom domain using Cloudflare DNS.

Project Overview
This project demonstrates:
    • Provisioning a cloud Linux server
    • Installing and configuring Nginx
    • Deploying a static HTML website
    • Configuring DNS records in Cloudflare
    • Troubleshooting Cloudflare Error 521

Architecture
Visitor Browser
      |
      v
Cloudflare DNS/CDN
      |
      v
Linode VPS (Ubuntu 24.04)
      |
      v
Nginx Web Server
      |
      v
/var/www/html/index.html

Server Details
    • Provider: Akamai Cloud (Linode)
    • OS: Ubuntu Server 24.04 LTS
    • Web Server: Nginx
    • Domain: pippins.us
    • Public IP: 50.116.26.150

1. Create a Linode Server
Create a new Ubuntu 24.04 LTS instance.
Recommended plan:
    • 1 Shared CPU
    • 1 GB RAM
    • 25 GB Storage
Deploy the instance and note the public IP address.

2. Connect to the Server
ssh root@50.116.26.150

3. Update the Server
apt update && apt upgrade -y

4. Install Nginx
apt install nginx -y
systemctl enable nginx
systemctl start nginx
Verify the service is running:
systemctl status nginx

5. Configure the Firewall
Install UFW if needed:
apt install ufw -y
Allow SSH and web traffic:
ufw allow OpenSSH
ufw allow 'Nginx Full'
ufw enable
Verify:
ufw status

6. Deploy the Website
Move your website files to the Nginx web root:
cp index.html /var/www/html/
Remove the default Nginx landing page:
rm /var/www/html/index.nginx-debian.html
Reload Nginx:
systemctl reload nginx

7. Verify by IP Address
Open:
http://50.116.26.150
Your website should load.

8. Configure Cloudflare DNS
In your Cloudflare DNS settings, create the following records.
Root Domain
Type
Name
Content
Proxy
A
@
50.116.26.150
Proxied
WWW Subdomain
Type
Name
Content
Proxy
CNAME
www
pippins.us
Proxied

9. DNS Verification
After propagation, these should resolve:
    • pippins.us
    • www.pippins.us
You can verify with:
dig pippins.us
dig www.pippins.us
Or use:
    • https://dnschecker.org

10. Cloudflare Error 521 Troubleshooting
Error 521 means Cloudflare cannot connect to the origin server.
Diagnostic Commands
systemctl status nginx
nginx -t
ufw status
curl -I http://localhost
curl -I http://50.116.26.150
Successful Results
    • Nginx service is active.
    • Nginx configuration test passes.
    • Firewall allows Nginx Full.
    • curl returns HTTP/1.1 200 OK.
If all of the above succeed, the issue is usually a DNS misconfiguration.

11. Optional Nginx Server Block (In Progress)
The default site works for a single website, but you can create a dedicated configuration.
Create:
nano /etc/nginx/sites-available/pippins.us
Add:
server {
    listen 80;
    server_name pippins.us www.pippins.us;

    root /var/www/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
Enable it:
ln -s /etc/nginx/sites-available/pippins.us /etc/nginx/sites-enabled/
nginx -t
systemctl reload nginx

12. Validation Checklist
    • Linode server created
    • Ubuntu updated
    • Nginx installed and running
    • UFW configured
    • Website deployed to /var/www/html
    • Site accessible by IP
    • Cloudflare A record created
    • Cloudflare CNAME record created
    • Domain loads successfully

Useful Commands
Restart Nginx:
systemctl restart nginx
Reload Nginx:
systemctl reload nginx
Test Nginx configuration:
nginx -t
Check listening ports:
ss -tulpn | grep -E ':80|:443'

Resume Project Description
Deployed a personal website to an Ubuntu server hosted on Akamai Cloud (Linode). Installed and configured Nginx, deployed static website files, configured Cloudflare DNS records, and troubleshot Cloudflare Error 521 to restore external connectivity.

Author
Charles Pippins
    • LinkedIn: https://www.linkedin.com/in/charlespippins
    • Website: https://pippins.us

License
This project is available under the MIT License.
