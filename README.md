

![images](images/Screenshot_1.png)

## Monitoring AWS EC2 using CloudWatch

In this project I manage AWS infrastucture using CloudWatch and CloudTrail, gaining insights into system perfomance, security and operational health.

### Step 1: Create an IAM Roe with CloudWatchFull Access and SSMFullAccess

  1. Navigate to the IAM console
  2. In the IAM Console navigation click on roles.



  3. Now create the images to create a role with `CloudWatchFullAccess` and `SSMFullAccess` policy

###  Step 2: Create a parameter in System Manager  

Now that wehave created an IAM role, we need to create a parameter in the system manager console. By doing this, we will be abl to define the metrics we want to monitor for our EC2 instance.

  1. Navigate to AWS System Manager Console
  2. In the AWS System Manager navigate menu, select parameter store
  3. Create a new parameter and paste the code snippet below.

  ```
  {
	"metrics": {
		"append_dimensions": {
			"InstanceId": "${aws:InstanceId}"
		},
		"metrics_collected": {
			"mem": {
				"measurement": [
					"mem_used_percent"
				],
				"metrics_collection_interval": 180
			},
            "disk": {
				"measurement": [
                     "disk_used_percent"
				],
				"metrics_collection_interval": 180
			}
		}
	}
} 
 
  ```

The parameter above are configuration file for the CloudWatch agent, which defines the metrics that will be collected from your EC2 instance and sent to CloudWatch.

 1. `"metrics"` : This is the top-leve key in the configuration file, indicating that it contains the definitions for the metrics to be collected.

 2. `"append_dimensions"` : This section specifies dimensions to be appended to all collected etrics. Dimensions are key-value pairs that help identify the source of the data in CloudWatch. In this case, the dimension "InstanceId" is appended, and its value populated dynamically with the instance ID of the EC2 instance where the CloudWatch agent is installed.

     - `"InstanceId" : "${aws:InstanceId}"` : This line specifies that the value of the "InstanceId" dimension should be dynamically populated with the instance ID of the EC2 instance.

3. `"metrics_collected"` :This section defines the specific metrics to be collected from the EC2 instance.

     - `"mem"` : This subsection specifies memory-related metrics to be collected.

        - `"measurement"` : This is an array specific memory metrics to collect. In this case only `mem_used_percent` is psecified, whic represents the percentage of memory used on the instance.
        - `"metrics_collection_interval"` : This parmeter specifies how frequently(in seconds) the metrics should be collected. Here, memory metrics will be collected every 60 seconds.

    - `"disk"` : This subsection specifies disk-related metrics to be collected

       - `"measurement"` : This is an array of specific disk metrics to collect. Only `"disk_used_percent"` is specified, representing the percentage of disk space used on the instance.
       - `"metrics_collection_interval"` : Similar to the memory sections, this parameter specifies how frequently disk metrics will be collected, which is every 60 seconds.


### Step 3:  Create an EC2 instance, attach the role created in Step 1

