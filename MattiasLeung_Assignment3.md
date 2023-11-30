# Mattias Leung (A01216455) Assignment 3 part 1
This tutorial will walk you through the steps of installing a new Debian server on DigitalOcean!

## Initial Setup on Digital Ocean
Ensure you have an account on Digital Ocean and are prepared to create a server.

### Generating an SSH Key
Secure SSH key generation:

1. Open your terminal.
2. Run `ssh-keygen`.
3. Create a directory for SSH keys: `mkdir .ssh`.
4. Generate a new SSH key pair: `ssh-keygen -t ed25519 -f .ssh/mydo_key -C "email@example.com"`.
5. **MacOS Users:** Copy your ssh key: `pbcopy` then `pbcopy < ~/.ssh/do-key.pub`
**Windows Users:** Copy your ssh key: `Get-Content C:\Users\user-name\.ssh\do-key.pub | Set-Clipboard`

### Creating a Droplet on Digital Ocean

1. Log in to [Digital Ocean](https://www.digitalocean.com/).
2. Click **Create** and select **Droplets**.
3. Choose **Debian** as your OS and configure other options.
4. Under **Authentication**, select **SSH Key** and paste your SSH key.

### Connecting to Your Droplet

1. Note your droplet's IP address from the dashboard.
2. Connect using `ssh -i path-to-your-key root@your_droplet_ip`.
3. Accept the server's identity prompt.

## Setting Up a Regular User on the Server

### User Creation and Configuration

1. Add a new user: `useradd -ms /bin/bash newusername`.
2. Set a password: `passwd newusername`.
3. Add the user to the sudo group: `usermod -aG sudo newusername`.

### Configuring SSH Access for the New User

1. Copy the `.ssh` directory: `cp -r /root/.ssh /home/newusername`.
2. Change ownership: `sudo chown -R newusername:newusername /home/newusername/.ssh`.
3. Exit and test the new user's SSH access: `ssh -i .ssh/mydo_key newusername@your_droplet_ip`.

### Disabling Root SSH Login

1. Edit SSH config: `sudo nano /etc/ssh/sshd_config`.
2. Change `PermitRootLogin yes` to `PermitRootLogin no`.
3. Restart SSH service: `sudo systemctl restart ssh.service`.

## Setting Up the Nginx Web Server

### Installing Nginx

1. Update packages: `sudo apt update`.
2. Install updates if available: `sudo apt upgrade`.
3. Install Nginx: `sudo apt-get install nginx`.
4. Enable Nginx: `sudo systemctl enable nginx`.

### Configuring Your Web Server

1. Navigate to the web directory: `cd /var/www`.
2. Create a site directory: `mkdir mysite`.
3. Create an `index.html` file: `sudo touch mysite/index.html`.
4. Edit the file and insert the following HTML content:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
        }
    </style>
    </head>
    <body>
    <h1>Hello, World</h1>
    </body>
    </html>
    ```

### Setting Up the Server Block for Nginx

1. Go to the Nginx sites-available directory: `cd /etc/nginx/sites-available`.
2. Create a new configuration file: `sudo nano mysite.conf` and enter the following server block configuration:

    ```nginx
    server {
    listen 80;
    listen [::]:80;

    root /var/www/my-site;

    index index.html index.htm index.nginx-debian.html;

    server_name _;

    location / {
        try_files $uri $uri/ =404;
        }
    }
    ```

3. Create a symbolic link in sites-enabled: `sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled`.
4. Remove the default link: `sudo unlink default`.
5. Test the Nginx configuration: `sudo nginx -t`.
6. Restart Nginx: `sudo systemctl restart nginx`.

### Testing the Server

1. Test access to your server from your local machine: `curl your_droplet_ip`.

## Completion
You have now successfully set up a Debian server on Digital Ocean and configured a basic web server using Nginx! 
