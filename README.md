# AUTO INSTANCE SCHEDULER PROJECT

### 1. Login to AWS Account with Root User/IAM User

### 2. Create an Instance of Your Choice (Linux, Windows, RedHat)
   - This will be the instance we will be auto starting and stopping.

### 3. Create Policies for Starting and Stopping EC2 Instances
   - Go to **IAM Role**, click on **Policies**.
   - Create policy `StartEC2Instance`:
     - Services: EC2
     - Actions: `DescribeInstances`, `StartInstances`
   - Create policy `StopEC2Instance`:
     - Services: EC2
     - Actions: `DescribeInstances`, `StopInstances`

### 4. Create Lambda Function 'AutoStart EC2'
   - Choose Runtime: **Python 3.12**
   - After creating the Lambda function, attach the policies to the role created with the Lambda function.
     1. Go to **Configuration** -> **Permissions**.
     2. Click on the role name (the name of the Lambda function).
     3. Click **Add Permission** -> **Attach Policies** -> Add the `StartEC2Instance` policy.
   - Go back to the Lambda function page -> **AutoStart EC2** -> **Code**, paste the `startec2.py` code and deploy.

### 5. Create Lambda Function 'AutoStop EC2'
   - Follow the same steps as in the previous step but attach the `StopEC2Instance` policy.
   - Paste `stopec2.py` in the code section and deploy.

### 6. Create a Rule in Amazon CloudWatch
   1. Click on **Rules** -> **Create Rule**.
   2. Provide a name: `StartEC2Rule` (you can also add an optional description).
   3. Select Rule Type: **Schedule**.
   4. Continue in **EventBridge Scheduler**.
   5. Scroll down to **Occurrence**, and click on **Recurring Schedule** -> Select your **Time Zone**.
   6. Set a cron-based schedule to start the EC2 instance (refer to the cron documentation: [AWS Cron Expressions](https://docs.aws.amazon.com/lambda/latest/dg/with-eventbridge-scheduler.html)).
      - Example: `10 10 ? JAN-DEC MON-SAT 2024` (Runs at 10:10 AM every Monday through Saturday in 2024).
   7. Click on **Next** -> **Templated Targets** -> **AWS Lambda Invoke**.
   8. Scroll down and choose the **AutoStart EC2** Lambda function.
   9. For **Action After Schedule Completion**: Select **None** -> Scroll down and click **Next**.
   10. Schedule creation is now complete.

### 7. Create a Schedule for 'AutoStop EC2'
   - Repeat the same steps as above but create a rule for stopping the EC2 instance.

### 8. Verify the Functions
   - Wait for the scheduled time and check if the functions are working as expected.
