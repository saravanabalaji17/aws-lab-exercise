# AWS EFS Hands-on Lab Exercise

**Goal:**
Create an EFS file system and mount it on **two EC2 instances** to demonstrate **shared storage**.

Example Resource Names:

* `studentname-efs`
* `studentname-ec2-1`
* `studentname-ec2-2`
* `studentname-efs-sg`

---

# 1️⃣ Architecture

```
             AWS VPC
                │
        ┌───────────────┐
        │  EFS File     │
        │ studentname-efs│
        └───────┬───────┘
                │
         Mount Targets (AZ)
          │             │
     ┌────┘             └────┐
     │                        │
┌──────────────┐      ┌──────────────┐
│ studentname  │      │ studentname  │
│  ec2-1       │      │  ec2-2       │
│              │      │              │
│ /efs-share   │◄────►│ /efs-share   │
└──────────────┘      └──────────────┘
        Shared File Storage
```

---

# 2️⃣ Lab Prerequisites

* AWS Account
* Default VPC
* 2 Public Subnets
* Key Pair for EC2

---

# 3️⃣ Step 1 — Create Security Group for EFS

Go to **EC2 → Security Groups → Create Security Group**

Name
`studentname-efs-sg`

Rules:

| Type | Port | Source   |
| ---- | ---- | -------- |
| NFS  | 2049 | VPC CIDR |

Example:

```
Type: NFS
Protocol: TCP
Port: 2049
Source: 172.31.0.0/16
```

---

# 4️⃣ Step 2 — Create EFS File System

Go to **EFS → Create File System**

Name
`studentname-efs`

Settings:

| Setting          | Value           |
| ---------------- | --------------- |
| VPC              | Default         |
| Storage class    | Standard        |
| Performance mode | General Purpose |
| Throughput       | Bursting        |
| Encryption       | Enabled         |

Next → **Network**

Add **Mount targets in all AZ**

Attach security group
`studentname-efs-sg`

Create File System.

---

# 5️⃣ Step 3 — Create EC2 Instance 1

Go to **EC2 → Launch Instance**

Name
`studentname-ec2-1`

Configuration:

| Setting       | Value          |
| ------------- | -------------- |
| AMI           | Amazon Linux 2 |
| Instance Type | t2.micro       |
| Key pair      | Your key       |
| VPC           | Default        |
| Subnet        | AZ1            |

Security group:

Allow

```
SSH 22
NFS 2049
```

---

# 6️⃣ Step 4 — Create EC2 Instance 2

Launch another instance

Name
`studentname-ec2-2`

Same configuration but choose **another AZ subnet**.

---

# 7️⃣ Step 5 — Connect to EC2

SSH to Instance 1

```
ssh -i key.pem ec2-user@public-ip
```

---

# 8️⃣ Step 6 — Install EFS Client

Run on **both EC2 instances**

```
sudo yum install -y amazon-efs-utils
```

---

# 9️⃣ Step 7 — Create Mount Directory

```
sudo mkdir /efs-share
```

---

# 🔟 Step 8 — Mount EFS

Get the **EFS DNS Name**

Example:

```
fs-xxxxxx.efs.ap-south-1.amazonaws.com
```

Mount:

```
sudo mount -t efs studentname-efs:/ /efs-share
```

or

```
sudo mount -t nfs4 fs-xxxxxx.efs.ap-south-1.amazonaws.com:/ /efs-share
```

---

# 1️⃣1️⃣ Step 9 — Add Permanent Mount (fstab)

Edit file

```
sudo vi /etc/fstab
```

Add

```
fs-xxxxxx.efs.ap-south-1.amazonaws.com:/ /efs-share efs defaults,_netdev 0 0
```

Test

```
sudo mount -a
```

---

# 1️⃣2️⃣ Step 10 — Create Data on EC2-1

On **studentname-ec2-1**

```
cd /efs-share
```

Create file

```
echo "Hello from EC2-1" > file1.txt
```

Create directory

```
mkdir app-data
touch app-data/app.log
```

Check

```
ls -l
```

---

# 1️⃣3️⃣ Step 11 — Validate from EC2-2

Login to **studentname-ec2-2**

```
cd /efs-share
```

Check files

```
ls -l
```

You should see

```
file1.txt
app-data
```

View file

```
cat file1.txt
```

Output

```
Hello from EC2-1
```

---

# 1️⃣4️⃣ Step 12 — Test Reverse Write

On **EC2-2**

```
echo "Hello from EC2-2" > file2.txt
```

Now check on **EC2-1**

```
ls /efs-share
```

You will see

```
file1.txt
file2.txt
app-data
```

This proves **EFS shared storage works across instances**.

---

# 1️⃣5️⃣ Validation Commands

Check mount

```
df -h
```

Check NFS mount

```
mount | grep efs
```

Check storage

```
ls -ltr /efs-share
```

---

# 1️⃣6️⃣ Expected Result

| Action                       | Result           |
| ---------------------------- | ---------------- |
| Create file on EC2-1         | Visible on EC2-2 |
| Create file on EC2-2         | Visible on EC2-1 |
| Both instances share storage | Yes              |

---

# 1️⃣7️⃣ Real-World Use Cases

* Kubernetes shared storage
* Web server shared content
* ML datasets
* Shared application logs
* CMS platforms (WordPress)

---

✅ **Lab Completed**

You successfully created:

* 1 EFS filesystem
* 2 EC2 instances
* Mounted shared storage
* Validated shared data

---
