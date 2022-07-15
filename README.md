# kasmweb-instructions
This will talk you through installing kasmweb on oracle cloud with cloudflare and lets encrypt for free forever

Sign up at oracle.com  
Click menu, compute, instances, create instance, Edit (next to Image and shape)  
Change shape to Ampare, A1.Flex, 4 OCPUs  
Change image to Canonical Ubuntu  
Tick Specify a custom boot volume size  
Type 100 in Boot volume size (GB)  
Click Save Private Key, put it in c:\users\YOURUSERNAME\.ssh\  
Click Save Public Key, put it in c:\users\YOURUSERNAME\.ssh\  
Click Create  
  
Wait for the Public IP address to appear on the right. Click Copy next to it.  
  
Click on Public Subnet  
Click on Default Security List for vcn  
Click Add Ingress Rules  
Source CIDR = 0.0.0.0/0  
Destination Port Range = 443  
Click Add Ingress Rule  
  
Sign up at cloudflare.com  
Domain Registration  
Register Domain  
Put in the domain name you'd like to register  
Once registered, click on the domain and click DNS  
Click Add Record  
Type = A  
Name = SUBDOMAIN for SUBDOMAIN.YOURDOMAIN.com or @ to put it at YOURDOMAIN.com  
IPv4 address (required) = PUBLIC IP ADDRESS FROM ORACLE  
Generate a token here: https://dash.cloudflare.com/profile/api-tokens  
Give it permission to Edit Zone DNS, select your domain  
  
In a new terminal, put the following:  
  
ssh ubuntu@YOURPUBLICIP -i c:\users\YOURUSERNAME\.ssh\YOURPRIVATEKEY  
cd /tmp  
curl -O https://kasm-static-content.s3.amazonaws.com/kasm_release_1.11.0.18142e.tar.gz  
tar -xf kasm_release*.tar.gz  
sudo bash kasm_release/install.sh -J 4096  
sudo snap install --classic certbot  
sudo ln -s /snap/bin/certbot /usr/bin/certbot  
sudo snap set certbot trust-plugin-with-root=ok  
sudo snap install certbot-dns-cloudflare  
echo 'dns_cloudflare_api_token = YOURCLOUDFLARETOKEN' > cloudflare.ini  
chmod 600 ./cloudflare.ini  
sudo certbot certonly --dns-cloudflare --dns-cloudflare-credentials /tmp/cloudflare.ini -d YOURDOMAIN  
sudo /opt/kasm/bin/stop  
sudo rm /opt/kasm/current/certs/kasm_nginx.crt  
sudo rm /opt/kasm/current/certs/kasm_nginx.key  
sudo ln -s /etc/letsencrypt/live/YOURDOMAIN/fullchain.pem /opt/kasm/current/certs/kasm_nginx.crt  
sudo ln -s /etc/letsencrypt/live/YOURDOMAIN/privkey.pem /opt/kasm/current/certs/kasm_nginx.key  
sudo /opt/kasm/bin/start  
  
Now you can go to https://SUBDOMAIN.YOURDOMAIN.com and it should ask for a username and password  
If you scroll up in the terminal a little, it should say:  
  
Kasm UI Login Credentials  
------------------------------------  
  username: admin@kasm.local  
  password: GENERATED PASSWORD  
    
Put these into the username and password fields in your browser  
Click Workspaces in the top  
Click one of the applications  
Click Launch  
