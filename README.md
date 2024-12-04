
## Assignment 3 Part 2

Before you go on ahead with the tasks, you first need to make a droplet, then a load balancer in order to start things. Otherwise you'll stuck in zone 1

# Creating a Droplet
Here are the steps in creating a droplet
```
Step 1. On DigitanOcean, to create a droplet. First click on Create, then Droplets.
Step 2. Then click on San Francisco 
Step 3. On Datacenter, click on San Francisco - Datacenter 3 - SFO3
Step 4. Go to Custom Images, select Arch Linux that is recently new and ends with .qcow2
Step 5. Make sure to select Premium AMD, then click $7/mo
Step 6. Select your SSH Key
Step 7. On Quantity, make sure to icrease the Droplet to 2, then name your Hostname (to whatever you perfer)
Step 8. Make sure to name your Tags, web
Step 9. Then, create Droplet 
```
Here is a picture of what it suppose to look like
![Screenshot 2024-12-03 231154](https://github.com/user-attachments/assets/59bf5562-8812-428f-838c-495a45f8a6f4)

----
You're done, now onto..
# Creating a load balancer

Here are the steps in creating a load balancer:

Step 1. If you look at your left bar of the screen, look for Networking and click on it, then click Create

Step 2. Go to Load Balancers, then click on Regional

Step 3. Select San Francisco - 3

![Screenshot 2024-12-03 233205](https://github.com/user-attachments/assets/d7c45a92-1bc4-4454-a58a-017feb7d4362)
Step 4. Once you reach, VPC Network, click on External (Public)
![Screenshot 2024-12-03 233242](https://github.com/user-attachments/assets/7243bea2-3e63-4c1e-bacb-0f50c4efac0b)
Step 5. Then, create Load Balancer

*Important* Everything else I didn't mention in the steps is to be left alone, including the create the droplet part

----
# Task 1
- First set up a system user webgen with a home directory by using the code below
```
sudo useradd -r -m -d /var/Lib/webgen -s /usr/sbin/nologin
```

- Before we go ahead, we must create a home directory 
```
sudo mkdir /var/Lib/webgen
```
After that, type `cd` and then the code above to go the main (or home) directory

- We will then set up the files to the directory 
```
sudo mkdir -p /var/Lib/webgen/bin
```
```
sudo mkdir -p /var/Lib/webgen/documents
```
```
sudo mkdir -p /var/lib/webgen/HTML
```
- after creating the files, git clone this:
```
sudo git clone https://git.sr.ht/~nathan_climbs/2420-as3-p2-start
```
Make sure to install git before cloning it
```
git pacman -s git
```
After cloning the repository, now move the file that is inside the repository 'generate_index' to the bin 
```
sudo mv 2420-as3-p2-start/generate_index bin
```
After that, you need to create an index.html file inside an HTML directory, you can use this code: 
```
sudo touch HTML/index.html
```
- Now to the last one, go to your documents. So 'cd documents' and create two files inside by typing, 
```
nvim Happiness1 (Could be any name you want)
```
```
nvim Happiness 2
```
Inside each of those files, just type in the same name as the file you named or any
- Make sure to delete the repository that you git cloned
```
sudo rm -rf 2420-as3-p2-start
```
- Then go back to the main one
```
cd ..
```
Then set the ownership
```
sudo chown -R webgen:webgen /var/lib/webgen
```
Now you're done, to see your structure, just type: 
```
sudo tree /var/lib/webgen
```
Here is the picture of what it suppose to look like:
![Screenshot 2024-12-03 224611](https://github.com/user-attachments/assets/1a72eb39-7da5-46ca-a5d6-869529ff38fa)
—
# Task 2
- We will now create a service file for both generate-index.service and generate-index. timer 
```
sudo nvim /etc/systemd/system/generate-index.service
```
```
sudo nvim /etc/systemd/system/generate-index.timer
```

This is what the generate-index.service file should look like
```
[Unit]
Description=The generate_index script 
Wants=network-online.target
After=network-online.target

[Service]
Type=oneshot
User=webgen
Group=webgen
ExecStart=/var/lib/webgen/bin/generate_index

[Install]
WantedBy=multi-user.target
```

This is what the generate-index.service should look like
```
[Unit]
Description=Run the generate_index at 5am on a daily basis

[Timer]
OnCalendar=*-*-* 05:00:00
Persistent=true

[Install]
WantedBy=timer.target
```

Now that it's done, make sure to run this command to reload
```
sudo systemctl daemon-reaload
```

After the command reload, we need to start and enable the services
```
sudo systemctl start generate-index.service
sudo systemctl enable generate-index.service

sudo systemctl start generate-index.timer
sudo systemctl enable generate-index.timer
```
You can check your services status by typing:
```
sudo systemctl status generate-index.service
```
Same thing for timer.
```
sudo systemctl status generate-index.timer
```
Picture of the result
![Screenshot 2024-12-03 225911](https://github.com/user-attachments/assets/3c94e7c1-3d73-4313-880e-1d26d9260e47)


----

# Task 3

For this task, we will have to install and configure nginx

- To install nginx type,
```
sudo pacman -S ngnix
```
- After that, create and edit the nginx.config file 
Before you go ahead, make sure to create directories so you won't face any issues later on.
```
sudo mkdir sites-available
```
```
sudo mkdir sites-enabled
```
----
Create this, 
```
sudo nvim /etc/nginx/nginx.conf
```
*Important* The only thing you need to edit in the ngnix.conf file is replace the #user http; (top left) with user webgen; and include the /etc/nginx/conf.d/*.conf directory above the include mine.types;
pictures below for example,
![Screenshot 2024-12-03 225112](https://github.com/user-attachments/assets/e69e7a0f-ade6-470b-9640-31adc192f5e4)

----

Now create a file for the server block file
```
sudo nvim /etc/nginx/conf.d/(However you name it, ex: task3_webgen).conf
```

Then add the code the file 
```
server {
    listen 80;
    listen [::]:80;
    
    server_name <Your IP address on Digital Ocean>;
    
    root /var/lib/webgen/HTML;
    index index.html;

    location /documents {
        root /var/lib/webgen;
        autoindex on;               
        autoindex_exact_size off;   
        autoindex_localtime on;      
    }

	location / {
        try_files $uri $uri/ =404;
    }
}
```

Now that you're done, you can test it by typing,
```
sudo nginx -t
```

You can also the type the command (below)
```
sudo systemctl daemon-reload
```

- Type this code, so it can start and enable
```
sudo systemctl start nginx
```
```
sudo systemctl enable nginx
```

to check if ngnix works, type:
```
sudo systemctl status nginx
```

----

# Task 4

We are going to install ufw, to start, type:
```
sudo pacman -S ufw
```

For the SSH and HTTP, type:
```
sudo ufw allow ssh
```
```
sudo ufw allow http
```

After that, we are going to set by limiting the SSH rate
```
sudo ufw limit ssh
```

To enable ufw, type:
```
sudo ufw enable
```

You can also see the status of your ufw by typing,
```
sudo ufw status verbose
```
Here is the picture of what it suppose to look,
![Screenshot 2024-12-03 225246](https://github.com/user-attachments/assets/1c28cf02-4a69-40e9-b1fb-4f130c24648a)

*Important* You will need to do these tasks for the two droplets you've made
