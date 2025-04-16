# Bakery_Foundation
Linux Machine Image Setup with Packer (Python 3.9 Compatible)

This guide walks you through creating a custom Amazon Machine Image (AMI) on Linux with Python 3.9 pre-installed, using HashiCorp Packer.
Requirements
1. Setting Up Your AWS Account

Before you begin, make sure you have an IAM user configured for Packer access:
Step 1: Create an IAM User

    Sign in to the AWS Console.

    Go to IAM (Identity and Access Management).

    Select Users > Add user.

    Set a username (e.g., packer-user) and enable Programmatic access.

    IAM Screenshot

Step 2: Assign IAM Policies

Attach the following policies:

    AmazonEC2FullAccess

    AmazonSSMManagedInstanceCore

    AmazonS3ReadOnlyAccess (optional)

Download and save your Access Key ID and Secret Access Key.
Step 3: Configure AWS CLI

    Install the AWS CLI:

sudo apt install awscli  # Ubuntu
brew install awscli      # macOS

Set up credentials:

    aws configure

2. Install Packer and AWS CLI

To get started, install both tools on your machine:
Linux/macOS

brew install packer       # macOS
sudo apt install packer   # Ubuntu

Alternatively, use the Windows Subsystem for Linux (WSL): WSL Screenshot
Why AWS CLI Is Needed

    Authentication: Easily handle credentials.

    Permissions: Manage IAM roles and policies.

    AWS Services Integration: Interacts with EC2, SSM, S3, etc.

To confirm it's working:

packer version

3. Create the Packer Template (bakery.json)

Below is a Packer template to set up a basic AMI with Python 3.9:

{
  "variables": {
    "aws_region": "us-east-1"
  },
  "builders": [
    {
      "type": "amazon-ebs",
      "region": "{{user `aws_region`}}",
      "source_ami": "ami-0c55b159cbfafe1f0",
      "instance_type": "t2.micro",
      "ssh_username": "ubuntu",
      "ami_name": "custom-python39-{{timestamp}}"
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "inline": [
        "sudo apt-get update",
        "sudo apt-get install -y python3.9 python3.9-venv python3.9-dev"
      ]
    }
  ]
}

4. Validate the Template

Run the following commands in the directory where bakery.json is saved:

packer init .
packer validate bakery.json

If there are no errors, you're ready to build.

Validation Screenshot
5. Build the AMI

To create your image, run:

packer build bakery.json

The process will take a few minutes. At the end, you'll receive the AMI ID for your new image.

Build Screenshot AMI Output
6. Launch an Instance Using Your Image
On AWS

    Go to EC2 Console > AMIs.

    Locate your new AMI.

    Launch an EC2 instance from it.

EC2 AMI Launch Launch Instance
Final Notes

This process ensures that every environment starts with a consistent Linux setup including Python 3.9. It helps streamline deployments and reduce configuration issues.

If you'd prefer a Docker-based image setup instead, feel free to ask! 🚀

    Created by Ansh Panwar

Let me know if you'd like a DOCX or PDF version of this too.
