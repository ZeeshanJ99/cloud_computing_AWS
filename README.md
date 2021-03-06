![Monolith architecture](https://user-images.githubusercontent.com/88186084/131873390-14a52f16-c301-42b1-8145-0ea20d099fb2.jpg)
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

`ssh -i "sre_key.pem" root@ec2-12-208-152-38.eu-west-1.compute.amazonaws.com`
- the ip changes everytime you launch a new instance

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


## reverse proxy
- in app
`sudo nano /etc/nginx/sites-available/default`

replace the location in the file, you can replace the port code 8080 to whatever you need it to be, e.g. 3000

    server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://localhost:3000;      
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade'; 
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;      
    }
            }

            
- `sudo nginx -t`
- `sudo systemctl restart nginx`
- restart the node app from the app location
- `npm start`
- check your browser without the port 3000


## Running the db
- Launch another instance: SRE_zeeshan_db
- Same process as app EC2 machine
- Only changes are in security group

1. Type: ssh, Source type: my ip
2. Type: Custom TCP, Source type: custom, port range: 27017, source: the app public ip /32


## provision the db
in db

`sudo nano /etc/mongod.conf`
ip `127.0.0.1` change to `0.0.0.0`

- restart mongo
- enable mongo
- check status


## Export DB_HOST env
- in app

`export DB_HOST=mongodb://3.250.140.143:27017/posts`

- the ip in this is the db public ip.


## Running the app
- cd into app

- inject the database into the app `node seeds/seed.js`

`npm install`

`npm start`

The app will run on the public IPV4 address. add /posts on the end of the public app ip to see posts

