# intrasonics_transmogrifier_bash_no_cron

## Task:<br>
Monitor the existing solution. The recently added features make the existing solution expected to behave in the following way: 
-  Independent processes will be started up to serve individual client requests. These should all be started by the transmogrifier user. 
-  Each of these processes may run for an extended period, depending on how much work is required to serve their specific request. This workload is expected to be highly variable. 
-  A dedicated Transmogrified/ folder has been created on each server, which these processes will occasionally write to. 

You have been asked to create a solution which monitors the following at regular intervals:<br>
-  Processes currently running on the machine. 
-  Contents of the Transmogrified/ folder. 
Write a script in the language of your choosing, which will be used to achieve the above. 


## Solution:

To monitor the running processes and the contents of the Transmogrified/ folder as described in the scenario, you can launch these two scripts:

<br>

#### 1. Main script1


<details markdown=1><summary markdown="span">Details</summary>

``` sh
#!/bin/bash

# Write script to monitor processes and folder contents
cat > /usr/local/bin/transmogrifier-monitor.sh << EOF

#!/bin/bash

while true; do
  printf "\n%s %s %s\n\n%s\n" "Processes lists for transmogrifier:" "$(hostname)" "$(date +"%Y-%m-%d %H:%M:%S")" "$(ps aux | grep root)" >> /var/log/transmogrifier_process.log
  printf "\n%s %s %s\n\n%s\n" "Flle list of transmogrifier:" "$(hostname)" "$(date +"%Y-%m-%d %H:%M:%S")" "$(ls -la /home/ec2-user/Transmogrified/)" >> /var/log/transmogrifier_files.log
  sleep 60
done

EOF
```
```
sudo chmod +x /usr/local/bin/transmogrifier-monitor.sh
```
</details>

#### 2. Script2 to start the main script1 on a boot


<details markdown=1><summary markdown="span">Details</summary>

``` sh
#!/bin/bash

# Start script on boot

# Create the systemd service file
cat > /etc/systemd/system/transmogrifier-monitor.service << EOF
[Unit]
Description=Transmogrifier Monitor

[Service]
ExecStart=/usr/local/bin/transmogrifier-monitor.sh
Restart=always
User=transmogrifier

[Install]
WantedBy=multi-user.target
EOF

# Reload systemd to pick up the new service file
systemctl daemon-reload

# Enable the service to start on boot
systemctl enable transmogrifier-monitor.service

# Start the service immediately
systemctl start transmogrifier-monitor.service

```
```
sudo chmod +x /usr/local/bin/transmogrifier-monitor.sh
```
</details>
