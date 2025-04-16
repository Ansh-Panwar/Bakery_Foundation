# Linux Machine Image Setup with Packer (Python 3.9 Compatible)

This guide walks you through creating a custom Amazon Machine Image (AMI) on Linux with Python 3.9 pre-installed, using HashiCorp Packer.

---

## Requirements

### 1. Setting Up Your AWS Account

Before you begin, make sure you have an IAM user configured for Packer access:

#### **Step 1: Create an IAM User**
1. Sign in to the [AWS Console](https://aws.amazon.com/console/).
2. Go to **IAM (Identity and Access Management)**.
3. Select **Users** > **Add user**.
4. Set a username (e.g., `packer-user`) and enable **Programmatic access**.

   ![IAM Screenshot](https://github.com/user-attachments/assets/cb5e90d7-be6f-400d-8903-07d774f11516)

#### **Step 2: Assign IAM Policies**
Attach the following policies:
- `AmazonEC2FullAccess`
- `AmazonSSMManagedInstanceCore`
- `AmazonS3ReadOnlyAccess` (optional)

Download and save your **Access Key ID** and **Secret Access Key**.

#### **Step 3: Configure AWS CLI**
1. Install the AWS CLI:
   ```sh
   sudo apt install awscli  # Ubuntu
   brew install awscli      # macOS
   ```
2. Set up credentials:
   ```sh
   aws configure
   ```

---

## 2. Install Packer and AWS CLI

To get started, install both tools on your machine:

### **Linux/macOS**
```sh
brew install packer       # macOS
sudo apt install packer   # Ubuntu
```

Alternatively, use the Windows Subsystem for Linux (WSL):
![WSL Screenshot](https://github.com/user-attachments/assets/12f7cb9c-7add-4e72-a1f9-968719b8de1c)

### Why AWS CLI Is Needed
- **Authentication**: Easily handle credentials.
- **Permissions**: Manage IAM roles and policies.
- **AWS Services Integration**: Interacts with EC2, SSM, S3, etc.

To confirm it's working:
```sh
packer version
```

---

## 3. Create the Packer Template (`bakery.json`)

Below is a Packer template to set up a basic AMI with Python 3.9:

```json
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
```

---

## 4. Validate the Template

Run the following commands in the directory where `bakery.json` is saved:

```sh
packer init .
packer validate bakery.json
```

If there are no errors, you're ready to build.

---

![Validation Screenshot](https://github.com/user-attachments/assets/3f1bae49-f3bc-4361-87ee-12f344320191)

---

## 5. Build the AMI

To create your image, run:

```sh
packer build bakery.json
```

The process will take a few minutes. At the end, you'll receive the AMI ID for your new image.

---

![Build Screenshot](https://github.com/user-attachments/assets/64f9526b-8112-43cb-9868-17fc0aa486cc)
![AMI Output](https://github.com/user-attachments/assets/0e8b9f8c-9aac-46a5-b694-c6cb5cc96d99)

---

## 6. Launch an Instance Using Your Image

### **On AWS**
1. Go to **EC2 Console** > **AMIs**.
2. Locate your new AMI.
3. Launch an EC2 instance from it.

![EC2 AMI Launch](https://github.com/user-attachments/assets/83ad6b03-d072-4b38-af5c-ef69db480332)
![Launch Instance](https://github.com/user-attachments/assets/7c95b4e2-386d-4ce3-a9a7-3bc68432641b)

---

## Final Notes

This process ensures that every environment starts with a consistent Linux setup including Python 3.9. It helps streamline deployments and reduce configuration issues.

If you'd prefer a Docker-based image setup instead, feel free to ask! 🚀

> Created by **Ansh Panwar**
