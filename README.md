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


<br>

## Solution:

To monitor the running processes and the contents of the Transmogrified/ folder as described in the scenario, you can launch these two scripts:


#### 1. Script transmogrifier-monitor.sh to write in intervals to .log files


<details markdown=1><summary markdown="span">Details</summary>

``` sh
#!/bin/bash

# This script is to run indefinitely and periodically log information about the transmogrifier process and its associated files.

while true; do  # Start an infinite loop

  # Log the list of processes running the transmogrifier command, along with the hostname and current date/time, to a file called transmogrifier_process.log
  printf "\n%s %s %s\n\n%s\n" "Processes lists for transmogrifier:" "$(hostname)" "$(date +"%Y-%m-%d %H:%M:%S")" "$(ps aux | grep transmogrifier)" >> /var/log/transmogrifier_process.log

  # Log the list of files in the Transmogrified directory, along with the hostname and current date/time, to a file called transmogrifier_files.log
  printf "\n%s %s %s\n\n%s\n" "Flle list of transmogrifier:" "$(hostname)" "$(date +"%Y-%m-%d %H:%M:%S")" "$(ls -la /home/transmogrifier/Transmogrified/)" >> /var/log/transmogrifier_files.log

  sleep 300  # Wait for 300 seconds (5 minutes) before running the loop again
done


```
</details>

#### 2. Commands to start the script in the background


<details markdown=1><summary markdown="span">Details</summary>

``` sh
# This line grants execute permission to the transmogrifier-monitor.sh script, allowing it to be run as a command
sudo chmod +x /usr/local/bin/transmogrifier-monitor.sh  

# This line runs the transmogrifier-monitor.sh script in the background as a root user using the Bash shell
sudo bash transmogrifier-monitor.sh &  

```
</details>

<br>

#### Afterwards, the logs can be set up to be sent to AWS CloudWatch by installing the agent to the controlled machine, for metrics monitoring, visualization and triggering notifications if needed
