# cloud_computing_AWS
## Instructions for setting up EC2 machine

In AWS, make sure the Ireland server is selected. Then proceed and navigate to Services, then to EC2 and launch an instance.

- click Launch instances
- Choose the AMI - select the Ubuntu 16.04 SSD volume type
- select next - configure instance details as t2 is fine
- select subnet and select the first one in the list (DevOps students 1a | default), enable auto assign public IP, next add storage
- leave storage as it is
- add tags - add name as the key: zeeshan, value = SRE_name_app
- configure security group - name:SRE_name_app  type: SSH, port range: 22, source: my IP, add your IP (google my IP if not known)
- Add a rule HTTP with 0.0.0.0 IP address to allow for access from any IP
- choose an existing key and launch the machine (e.g. sre_key.pem) THESE KEYS MUST NEVER BE SHARED ON THE CLOUD MAKE SURE TO NOT PUSH THIS FILE

## Adding port 3000
- Navigate to security groups and add the inbound rule. The port should be `3000` and the source as `Anywhere IPV4` 


## Access the machine through the terminal
Get the key and save it into a memorable directory that you WILL NOT UPLOAD ANYWHERE
Copy the chmod line for the first time and then place ssh line into the git bash terminal where the app is located. 

`ssh -i "sre_key.pem" ubuntu@ec2-3-250-72-105.eu-west-1.compute.amazonaws.com`

## Provisioning
Follow these instructions to provision the ec2 machine  
Provisioning is very similar to the way the locally hosted app machine was provisioned:

`sudo apt-get update -y`

`sudo apt-get upgrade -y`

`sudo apt-get install nginx -y`

`sudo apt-get install npm -y`

`sudo apt-get install python-software-properties`

`curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -`

`sudo apt-get install nodejs -y`

`sudo npm install pm2 -g -y`
    

## Syncing app folder
This is completed from the localhost terminal (where the app folder was created)

`scp -ri ~/.ssh/sre_key.pem /app ubuntu@:3.250.72.105/home/ubuntu/app`



## Running the app
- cd into app

`npm install`

`npm start`

The app will run on the public IPV4 address with the port 3000
