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

After the command reload, we need to start 
