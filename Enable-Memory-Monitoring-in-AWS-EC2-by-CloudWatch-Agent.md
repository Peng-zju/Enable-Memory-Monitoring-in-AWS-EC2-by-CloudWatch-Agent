# Enable-Memory-Monitoring-in-AWS-EC2-by-CloudWatch-Agent

Below are the simplified instructions for enabling memory monitoring by Installing CloudWatch Agent.

**Step1 - Create IAM roles that enables the agent to collect metrics**

- AWS console -> IAM -> Roles -> Create Roles
- Make sure that **AWS service** is selected under Select type of trusted entity.
- For Choose a use case, choose **EC2** under Common use cases,
- Choose Next: Permissions.
- Select the checkbox next to **CloudWatchAgentServerPolicy**. If necessary, use the search box to find the policy.
- Choose Next: Review.
- Confirm that CloudWatchAgentServerPolicy appears next to Policies. In the Role name, enter **CloudWatchAgentServerRole**. Then choose Create role.

**Step 2 - Install CloudWatch Agent on EC2** 
- SSH into EC2
- For Centos (other OS requires different [download link](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Agent-commandline-fleet.html)) 

- `wget https://s3.amazonaws.com/amazoncloudwatch-agent/centos/amd64/latest/amazon-cloudwatch-agent.rpm`

- `sudo rpm -U ./amazon-cloudwatch-agent.rpm`


**Step 3 - Create the Agent Configuration File**
- Download [memMonitorConfig.json](https://github.com/Peng-zju/Enable-Memory-Monitoring-in-AWS-EC2-by-CloudWatch-Agent/blob/main/memMonitorConfig.json) and upload to current directory in EC2

**Step 4 - Attach IAM Role to a specific EC2** 
- Open the Amazon EC2 console 
- In the navigation pane, choose Instances.
- Select the instance, choose **Actions, Security, Modify IAM role.**
- Select the CloudWatchAgentServerRole to attach to your instance, and choose Save.

**Step 5 - Start the CloudWatch Agent**
- SSH into EC2
- `sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -s -c file:memMonitorConfig.json`
- Verify CloudWatch Agent is running on EC2 
`sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -m ec2 -a status`

**Step 6 - Check Memory Usage in AWS console**
- In AWS console -> CloudWatch -> Cutsom Namespaces -> CWAgent
- Check mem_used_percent metric


**When to resize the instance?**

Suggestion from AWS doc:

Identify instances with a maximum CPU usage and memory usage of less than 40% over a four-week period. These are the instances that you will want to right size to reduce costs.
