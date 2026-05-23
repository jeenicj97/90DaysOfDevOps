## Launch a cloud server, connect through SSH, install Nginx, configure web access, and collect logs.

# Commands Used:
## Connect via SSH
ssh -i udaan-batch-server-key.pem ubuntu@34.217.191.124
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/d56a88bed0fcb6e191fb09d0a412024706955560/2026/day-08/ec2.png)

![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/d56a88bed0fcb6e191fb09d0a412024706955560/2026/day-08/ssh-conn.png)

## Update System
sudo apt-get update

## Install Docker & Nginx
sudo apt-get install docker.io
sudo apt-get install nginx

## Check Nginx Status
systemctl status nginx

## After allowing Port 80 Http traffic in Security Group of EC2
![Image Alt](https://github.com/jeenicj97/90DaysOfDevOps/blob/d56a88bed0fcb6e191fb09d0a412024706955560/2026/day-08/nginx-web.png)

## View Logs
sudo tail -20 /var/log/nginx/access.log

## Save Logs
sudo cp /var/log/nginx/access.log ~/nginx-log.txt

![Image ALt](https://github.com/jeenicj97/90DaysOfDevOps/blob/d56a88bed0fcb6e191fb09d0a412024706955560/2026/day-08/nginx-log.png)

# Challenges Faced
  SSH key (.pem) had "unprotected private key" error due to WSL/Windows 
  filesystem not supporting Linux permissions - fixed by moving the key 
  to WSL home directory and running chmod 400 (while downloading log file to local)
  Command:
  - cp /mnt/c/Users/Jeeni/Downloads/udaan-batch-server-key.pem ~/udaan-batch-server-key.pem
  chmod 400 ~/udaan-batch-server-key.pem
  - scp -i ~/udaan-batch-server-key.pem ubuntu@34.217.191.124:~/nginx-access-log.txt .

# What I Learnt

- How to launch and access cloud servers
- How SSH authentication works
- How to install and manage services
- How to check logs for troubleshooting, copying & downloading them
- Importance of firewall/security group rules


