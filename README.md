         ___        ______     ____ _                 _  ___  
        / \ \      / / ___|   / ___| | ___  _   _  __| |/ _ \ 
       / _ \ \ /\ / /\___ \  | |   | |/ _ \| | | |/ _` | (_) |
      / ___ \ V  V /  ___) | | |___| | (_) | |_| | (_| |\__, |
     /_/   \_\_/\_/  |____/   \____|_|\___/ \__,_|\__,_|  /_/ 
 ----------------------------------------------------------------- 


Hi there! Welcome to AWS Cloud9!

To get started, create some files, play with the terminal,
or visit https://docs.aws.amazon.com/console/cloud9/ for our documentation.

Happy coding!
# packer_create_AMI


Using Packer to Create an AMI
Introduction
In this hands-on lab, you are a DevOps Engineer that works for OmniCorp. Your team has an AWS account where you deploy out your dev and test environments. After having to create AMIs manually, your team has decided that it’s time to automate the process. You are given access to Cloud9 where you will use Packer to create a base AMI for the team.
Solution
Log in to the AWS Console using the credentials provided on the hands-on lab page.
Install Packer on the Cloud9 EC2 Instance
1.	Navigate to the Cloud9 service
2.	Click Open IDE
3.	Obtain the Packer download link:
o	In a new tab, navigate to https://packer.io/downloads.html
o	Right-click the 64-bit link in the Linux section and choose Copy link address
4.	In the Cloud9 tab in your browser, run the following commands in the terminal window:
o	Become the root user:
  sudo su
o	Change directory to /usr/local/bin:
  cd /usr/local/bin
o	Download the Packer installer:
  wget LINK_FROM_STEP_3
o	Extract the file:
  unzip packer_1.2.4_linux_amd64.zip
o	Remove the Packer ZIP file:
  rm packer_1.2.4_linux_amd64.zip
o	Exit the root user session:
  exit
o	Verify that Packer works
  packer --version
Create a packer.json file
1.	In Cloud9, click File > New File
2.	Click File > Save As
3.	Name your file "packer.json"
4.	Provide the following JSON for your file:
Note: Be sure to replace [USERNAME] with a username of your choosing. The instructor used his first initial and last name, tthomsen.
 {
   "variables": {
     "instance_size": "t2.small",
     "ami_name": "ami-[USERNAME]",
     "base_ami": "ami-1853ac65",
     "ssh_username": "ec2-user",
     "vpc_id": "",
     "subnet_id": ""
   },
   "builders": [
     {
       "type": "amazon-ebs",
       "region": "us-east-1",
       "source_ami": "{{user `base_ami`}}",
       "instance_type": "{{user `instance_size`}}",
       "ssh_username": "{{user `ssh_username`}}",
       "ssh_timeout": "20m",
       "ami_name": "{{user `ami_name`}}",
       "ssh_pty" : "true",
       "vpc_id": "{{user `vpc_id`}}",
       "subnet_id": "{{user `subnet_id`}}",
       "tags": {
         "Name": "App Name",
         "BuiltBy": "Packer"
       }
     }
   ],
   "description": "AWS image",
   "provisioners": [
     {
       "type": "shell",
       "inline": [
         "sudo yum update -y",
         "sudo yum install -y git"
       ]
     }
   ]
 }
5.	Validate the packer.json file. Run the following command in the terminal window at the bottom of the page:
 packer validate packer.json
Build an AMI using packer.json
1.	We need to view a more current AMI for our base_AMI variable. To find this information:
o	Switch back to the AWS Console tab in your browser
o	Navigate to the EC2 service
o	Click Launch Instance
o	Copy the AMI ID at the end of the "Amazon Linux AMI" line
2.	For our vpc_id:
o	Navigate to the VPC service
o	Click Your VPCs
o	Copy the VPC ID
3.	For our subnet_id:
o	Click Subnets
o	Check the box to the left of the name on the first subnet in the list
o	Ensure that Auto-assign Public IP is "yes" for this subnet
o	Copy the Subnet ID
4.	We will use these values to populate our next command:
 packer build -var 'ami_name=ami-[USERNAME]' -var 'base_ami=[AMI_ID]' -var 'vpc_id=[VPC_ID]' -var 'subnet_id=[SUBNET_ID]' packer.json
5.	Once the command has completed, copy the AMI ID from the output
6.	In the AWS Console tab in your browser, navigate to the EC2 service again
7.	Click AMIs to verify your new AMI is listed
Build an EC2 Instances using the AMI
1.	Click Launch
2.	Check the box to select a t2.small instance type
3.	Click Next: Configure Instance Details
4.	Click Next: Add Storage
5.	Click Next: Add Tags
6.	Add a tag:
o	Key: Name
o	Value: test-ami
7.	Click Next: Configure Security Group
8.	Click Review and Launch
9.	Click Launch
10.	Choose Proceed without a key pair and check the box to acknowledge
11.	Click Launch Instances
12.	In the next screen you should see a green box saying "Your instances are now launching". Click the instance ID number provided next to the text "The following instance launches have been initiated:"
13.	Watch your AMI progress to a "Running" instance state. You may need to click the Refresh icon in the top-right to show the updated state.

