# Tech 258 - Deploying Sparta Test Application

## How do we securely transfer files to the Virtual Machine?

There are two different methods: <br>
1) Using SCP
2) Cloning public GitHub Repo

### Method 1 - Using SCP
This command uses the Secure Copy Protocol (SCP) to enable efficient and secure transfer files between two systems over a network. It will use SSH to authenticate and encrypt the data.

Example usage: <br>
```
scp -i /c/Users/username/.ssh/private_key -r /path/to/local/folder username@public-ip:/path/to/destination
```

Breaking down the command flags:

- `-i`: As SCP uses SSH to authenticate, we will have to reference our SSH private key.
- `-r`: Recursively copy the directory and its contents.

### Method 2 - Cloning GitHub Repo
Pre-requisites: <br>
1) Create a new GitHub repository.
2) Push the app to the repository.
3) SSH in the Virtual Machine
4) Run the following command to clone the repository.
```
git clone <github_repo_link.git>
```

## Running the Application
1) You will need to make sure you are in the `app` directory using the `cd` command.
2) Install node.js v20.
3) Install the app using `npm install`.
4) Running the app using `npm start`.

## Automation
Once each step has been verified manually, we can make a Bash script to automate this process:

```
#!/bin/bash
 
# update
echo updating...
sudo apt update -y
echo update complete!
 
# upgrade
echo upgrading...
sudo DEBIAN_FRONTEND=noninteractive apt upgrade -y
echo upgrade complete!
 
# install nginx
echo installing nginx...
sudo DEBIAN_FRONTEND=noninteractive apt install nginx -y
echo nginx install complete!
 
# restart nginx
echo restarting nginx...
sudo systemctl restart nginx
echo nginx restarted!

# enable nginx
echo enabling nginx...
sudo systemctl enable nginx
echo nginx enabled!

## install node.js v20
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo DEBIAN_FRONTEND=noninteractive -E bash - && sudo DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs
echo Node.js v20 installed!

echo check node js version
node -v
echo done!

# get the app folder with the app code inside
echo Cloning github repo...
git clone https://github.com/ShafiqueMahen2/tech258_sparta_test_app.git
echo Cloning complete!

# cd app folder
echo changing to app directory...
cd ~/tech258_sparta_test_app/app
echo done!

# install app
echo installing node js app..
npm install
echo node js app has been installed!

# install pm2
echo installing pm2...
sudo npm install pm2 -g
echo pm2 has been installed!

# stop existing 'app' node process
echo stopping existing 'app' node process...
pm2 stop app
echo existing node processes terminated!

# run app (with pm2)
echo running node js app with pm2...
pm2 start app.js app
```

