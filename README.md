
# 💻 DigitalOcean DevOps Practice Project

This project documents my hands-on journey into core DevOps practices using **DigitalOcean droplets**. The goal was to simulate real-world deployment and infrastructure tasks to become production-ready for a startup DevOps role.


## 🚀 Project Objectives

✅ Create and SSH into a DigitalOcean Droplet
✅ Install and configure NGINX
✅ Set up a firewall (UFW)
✅ Write basic Bash scripts and automate them with `cron`
✅ Install and use `doctl` (DigitalOcean CLI)
✅ Learn snapshot/backup basics
✅ Destroy and recreate droplets
✅ Install and use Netdata for monitoring
✅ Troubleshoot and document all errors encountered


## 🛠️ Tools Used

* **DigitalOcean**
* **Ubuntu 22.04 Droplet**
* **NGINX**
* **UFW (Uncomplicated Firewall)**
* **Bash scripting**
* **Cron jobs**
* **Netdata**
* **doctl CLI**
* **Snapshot & Backup**
* **SSH**


## 🔧 Step-by-Step Implementation

### 1. Creating and SSHing into a Droplet

* Created a droplet from the DigitalOcean dashboard (Ubuntu 22.04, 512MB RAM).
* SSHed into the droplet using:

  ```bash
  ssh root@<droplet-ip>
  ```


### 2. Installing and Configuring NGINX

* Installed NGINX:

  ```bash
  sudo apt update && sudo apt install nginx -y
  ```
* Verified it's running:

  ```bash
  systemctl status nginx
  ```


### 3. Setting Up Firewall (UFW)

* Enabled UFW and allowed SSH and HTTP:

  ```bash
  sudo ufw allow 'OpenSSH'
  sudo ufw allow 'Nginx Full'
  sudo ufw enable
  ```


### 4. Installing and Authenticating doctl (DigitalOcean CLI)

* Installed via Snap:

  ```bash
  sudo snap install doctl
  ```
* Created a DigitalOcean API Token and authenticated:

  ```bash
  doctl auth init
  ```


### 5. Automating Snapshots with Bash and Cron

#### Snapshot Script (`snapshot.sh`)

```bash
#!/bin/bash

DROPLET_NAME="ossai-dev"
DROPLET_ID="508583942"  # Replace with your actual droplet ID
TIMESTAMP=$(date +%Y-%m-%d-%H%M)
SNAPSHOT_NAME="${DROPLET_NAME}-snapshot-${TIMESTAMP}"

echo "Creating snapshot: $SNAPSHOT_NAME for droplet ID: $DROPLET_ID"
doctl compute droplet-action snapshot $DROPLET_ID --snapshot-name "$SNAPSHOT_NAME"
```

#### Cron Job Setup

* Added to `crontab`:

  ```bash
  crontab -e
  ```

  Example to run every day at 2 AM:

  ```
  0 2 * * * /root/snapshot.sh
  ```


### 6. Monitoring with Netdata

* Installed using:

  ```bash
  bash <(curl -SsL https://my-netdata.io/kickstart.sh)
  ```

* Exposed Netdata to public IP and accessed it at:

  ```
  http://<droplet-ip>:19999
  ```

* ⚠️ Solved issue where Netdata wouldn’t load:

  * Opened port 19999 in UFW:

    ```bash
    sudo ufw allow 19999
    ```


### 7. Droplet Destruction and Recreation

#### Backup via Snapshot

* Snapshot created before destroying:

  ```bash
  doctl compute droplet-action snapshot <droplet-id> --snapshot-name "my-snapshot"
  ```

#### Droplet Destruction

* Safe destruction:

  ```bash
  doctl compute droplet delete <droplet-id>
  ```

#### Recreate from Snapshot

* Created a new droplet from snapshot:

  ```bash
  doctl compute droplet create ossai-dev \
    --region fra1 \
    --image <snapshot-id> \
    --size s-1vcpu-512mb-10gb \
    --ssh-keys <your-key-id> \
    --enable-monitoring
  ```


## 🧩 Problems Encountered & Solutions

`doctl: command not found`--solution= Installed via `sudo snap install doctl`
Snapshot script failed    --solution= Explicitly set `DROPLET_ID` instead of auto-fetch
Netdata didn’t connect     --solution=Opened port 19999 on UFW
`--resource-type` flag error --solution=Used `--resource droplet` instead
Lost API token               --solution=Generated a new one via DigitalOcean dashboard and re-authenticated
NGINX not accessible         --solution=Firewall was blocking ports — fixed by allowing HTTP/HTTPS



## 🧠 Key Learnings

* Practical experience with cloud provisioning and automation
* Understanding of how backups/snapshots work in real-world scenarios
* Confidence using CLI tools like `doctl`
* Importance of firewall configuration and access control
* Learned to use cron for automation
* How to expose and secure monitoring tools like Netdata


## ✅ Next Aims

* Domain setup and securing with SSL 
* Full CI/CD pipeline using GitHub Actions
* Integration with monitoring alerts
* Cost optimization (droplet downsizing, cleanup)


## 📁 Project Status: **Complete**

This project lays the groundwork for more advanced DevOps practices. All scripts and automation files are included in this repository.
