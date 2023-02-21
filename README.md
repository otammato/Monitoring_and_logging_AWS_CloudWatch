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

#### Afterwards, the logs can be set up to be regularly sent to AWS CloudWatch by installing the CloudWatch agent to the controlled machine, for metrics monitoring, visualization and triggering notifications if needed

<details markdown=1><summary markdown="span">This is how to set up</summary>
<br>

To configure the CloudWatch agent to send the required logs to CloudWatch, follow these steps:

1. SSH into the EC2 instance hosting the servers that will run the Transmogrifier.

2. Download and install the CloudWatch Logs agent on the instance. 
``` sh
sudo curl https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm -O

sudo rpm -U ./amazon-cloudwatch-agent.rpm
```

3. Once installed, open the CloudWatch Logs agent configuration file located at /etc/awslogs/awslogs.conf.

4. Add log files that you want to monitor to the configuration file, specifying the log file location, log format, and destination log group in CloudWatch. Here is an example configuration entry:
``` sh
[/var/log/transmogrifier_process.log]
datetime_format = %Y-%m-%d %H:%M:%S
log_stream_name = {instance_id}
log_group_name = transmogrifier-log-group
```
In this example, we're monitoring the /var/log/transmogrifier_process.log file and sending its contents to a log group named transmogrifier-log-group in CloudWatch. The log_stream_name parameter will automatically include the instance ID in the log stream name, allowing you to distinguish between logs from different instances.

5. Once you have added all the log files you want to monitor, save the configuration file and restart the CloudWatch Logs agent with the following command:

```
sudo service awslogs restart
```

</details>
