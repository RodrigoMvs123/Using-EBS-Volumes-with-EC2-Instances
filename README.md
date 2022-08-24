# Using-EBS-Volumes-with-EC2-Instances

https://www.youtube.com/watch?v=EdRE1EH_G-4

Using EBS Volumes with EC2 Instances 

https://www.youtube.com/watch?v=EdRE1EH_G-4

https://aws.amazon.com/amazon-linux-2/
[ec2-user@ip-172-31-81-104~] $ lsblk 

[ec2-user@ip-172-31-81-104~] $ sudo file -s /dev/xvdf
[ec2-user@ip-172-31-81-104~] $ sudo mkfs -t xfs /dev/xvdf
[ec2-user@ip-172-31-81-104~] $ sudo file -s /dev/xvdf 
[ec2-user@ip-172-31-81-104~] $ cd / demo
[ec2-user@ip-172-31-81-104 demo ] $ ls
[ec2-user@ip-172-31-81-104 demo ] $ cd data 
[ec2-user@ip-172-31-81-104 data ] $ ls
[ec2-user@ip-172-31-81-104 data ] $ sudo mount /dev/ xvdf /demo
[ec2-user@ip-172-31-81-104 data ~] $ cd / demo
[ec2-user@ip-172-31-81-104 demo ] $ ls
[ec2-user@ip-172-31-81-104 demo ] $ sudo mkdir data
[ec2-user@ip-172-31-81-104 sudo ] $ cd data
[ec2-user@ip-172-31-81-104 data ] $ echo ‘{“topics”: []}’ | tee “/demo/data/data-storage.json”
[ec2-user@ip-172-31-81-104 data] $ echo ‘{“topics”: []}’ | sudo tee “/demo/data/data-storage.json”
[ec2-user@ip-172-31-81-104 data ] $ ls
[ec2-user@ip-172-31-81-104 data ] $



https://aws.amazon.com/amazon-linux-2/
[ec2-user@ip-172-31-85- 46~] $ sudo mount /dev/xvdf /demo
[ec2-user@ip-172-31-85- 46~] $ cd demo
[ec2-user@ip-172-31-85- 46 demo ] $ ls
[ec2-user@ip-172-31-85- 46 demo ] $ cd data 
[ec2-user@ip-172-31-85- 46 data ] $ cat data-storage.json
[ec2-user@ip-172-31-85- 46 data ] $    




#!/bin/bash

# Enable logs
exec > >(tee /var/log/user-data.log|logger -t user-data -s 2>/dev/console) 2>&1

# Install Git
echo "Installing Git"
yum update -y
yum install git -y

# Install NodeJS
echo "Installing NodeJS"
touch .bashrc
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
. /.nvm/nvm.sh
nvm install --lts

# Clone website code
echo "Cloning website"
mkdir -p /demo-website
cd /demo-website
git clone https://github.com/academind/aws-demos.git .
cd dynamic-website-basic

# Install dependencies
echo "Installing dependencies"
npm install

# Create data directory (later => EBS)
echo "Creating data directory & file"
mkdir -p /demo/data
echo '{"topics": []}' | tee "/demo/data/data-storage.json"

# Forward port 80 traffic to port 3000
echo "Forwarding 80 -> 3000"
iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 3000

# Install & use pm2 to run Node app in background
echo "Installing & starting pm2"
npm install pm2@latest -g
pm2 start app.js
