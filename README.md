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

  ![Screenshot from 2025-03-18 17-06-02](https://github.com/user-attachments/assets/e087459e-ab82-4730-8cf1-509fe9dbb50c)


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

![Screenshot from 2025-03-18 17-21-43](https://github.com/user-attachments/assets/53b449ad-cf1b-4532-9629-b8f539c234d3)


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

![Screenshot from 2025-03-18 17-21-49](https://github.com/user-attachments/assets/13daa60f-01e7-49e2-ab38-625846c44ae2)


---

## 5. Build the AMI

To create your image, run:

```sh
packer build bakery.json
```

The process will take a few minutes. At the end, you'll receive the AMI ID for your new image.

---

![Screenshot from 2025-03-18 17-22-22](https://github.com/user-attachments/assets/b3bef108-7238-41f4-adf8-10aaf8053abb)

![Screenshot from 2025-03-18 17-22-31](https://github.com/user-attachments/assets/19a35dab-950a-43c2-9f8c-275c2196eecc)


---

## 6. Launch an Instance Using Your Image

### **On AWS**
1. Go to **EC2 Console** > **AMIs**.
2. Locate your new AMI.
3. Launch an EC2 instance from it.

![Screenshot from 2025-03-18 17-19-59](https://github.com/user-attachments/assets/d78f04b2-fba5-429d-b733-87be0eab7c20)

![Screenshot from 2025-03-18 17-19-44](https://github.com/user-attachments/assets/987f3261-b438-4451-9919-ca3a241eb7bd)


---

## Final Notes

This process ensures that every environment starts with a consistent Linux setup including Python 3.9. It helps streamline deployments and reduce configuration issues.

If you'd prefer a Docker-based image setup instead, feel free to ask! 🚀

> Created by **Ansh Panwar**
