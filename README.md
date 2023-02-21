# intrasonics_transmogrifier_bash_no_cron

# intrasonics_transmogrifier_CloudFormation

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

To monitor the running processes and the contents of the Transmogrified/ folder as described in the scenario, you can use AWS CloudWatch with the following steps:

<br>
#1. Create a custom CloudWatch log group: Create a custom log group in CloudWatch where the logs for the script will be stored. You can do this from the CloudWatch console by selecting "Log Groups" and then clicking the "Create Log Group" button.
<br><br>
<details markdown=1><summary markdown="span">Details</summary>

Here are the steps to create a custom CloudWatch log group:

1. Open the AWS Management Console and navigate to the CloudWatch service.
2. In the CloudWatch dashboard, select "Log Groups" from the left-hand navigation menu.
3. Click the "Create Log Group" button in the top-right corner of the page.
4. In the "Create Log Group" dialog box, enter a name for the log group in the "Log Group Name" field. For example, you can name it "Transmogrifier Logs".
5. You can optionally add tags to the log group by clicking the "Add Tags" button. Tags are key-value pairs that help you organize and search for log groups.
6. Click the "Create" button to create the log group.

Your custom CloudWatch log group is now created and ready to receive logs from your script.
</details>
