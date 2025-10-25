# Complete-DevOps-Project-Multi-Tier-Application-Deployment-Locally

This project sets up a complete multi-tier architecture using Vagrant + VirtualBox, featuring MySQL, Memcache, RabbitMQ, Tomcat, and Nginx servers.
Each service is provisioned manually for learning and demonstration purposes on Windows systems.

## Project Overview
Architecture
VM Name	Role	Technology	OS
db01	Database	MariaDB (MySQL)	CentOS Stream 9
mc01	Caching	Memcache	CentOS Stream 9
rmq01	Messaging Queue	RabbitMQ	CentOS Stream 9
app01	Application	Tomcat + Java	CentOS Stream 9
web01	Web Server	Nginx	Ubuntu 22.04

## Step 0: Prerequisites
Install Required Tools
Tool	Download Link
VirtualBox	Download VirtualBox

Vagrant	Download Vagrant

Git	Download Git for Windows
Optional Plugin
vagrant plugin install vagrant-hostmanager

## Step 1: Clone the Repository

Open **Git Bash** or **CMD** and run:

```bash
cd C:\Users\tikke\Downloads
git clone https://github.com/Muncodex/digiprofile-project.git
cd digiprofile-project
git checkout local-vagrant
```

> If the folder already exists, delete it or run:

```bash
git pull
```

---
<img width="1920" height="1080" alt="Screenshot (672)" src="https://github.com/user-attachments/assets/3c70a7b8-b27e-42e1-9055-7fa4baa734a1" />
<img width="1920" height="1080" alt="Screenshot (673)" src="https://github.com/user-attachments/assets/bda6c45f-36fa-416a-be94-a04888c3ab35" />
<img width="1920" height="1080" alt="Screenshot (674)" src="https://github.com/user-attachments/assets/f79b6df3-4aac-4c5c-bad7-834b9052c9a8" />


## Step 2: Check & Fix Vagrantfile for Windows

Vagrantfile path:

```
digiprofile-project\vagrant\Manual_provisioning_Windows\Vagrantfile
```

Open it in **VS Code** or **Notepad** and make sure the following boxes exist:

```ruby
db01.vm.box = "eurolinux-vagrant/centos-stream-9"
mc01.vm.box = "eurolinux-vagrant/centos-stream-9"
rmq01.vm.box = "eurolinux-vagrant/centos-stream-9"
app01.vm.box = "eurolinux-vagrant/centos-stream-9"
web01.vm.box = "ubuntu/jammy64"
```

> Replace any invalid base boxes with the ones above.

---
<img width="1920" height="1080" alt="Screenshot (675)" src="https://github.com/user-attachments/assets/8ccd52a5-2243-41ff-ad9c-b7d5a9594938" />

## Step 3: Navigate to the Vagrantfile Folder

```bash
cd C:\Users\tikke\Downloads\digiprofile-project\vagrant\Manual_provisioning_Windows
```

---
<img width="1920" height="1080" alt="Screenshot (660)" src="https://github.com/user-attachments/assets/64e60338-9995-4922-aebe-617ec5346968" />

## Step 4: Start the Virtual Machines

Run:

```bash
vagrant up
```

This will:

* Download the boxes (if not already available)
* Create **5 VMs**:

  * `db01` → MySQL
  * `mc01` → Memcache
  * `rmq01` → RabbitMQ
  * `app01` → Tomcat
  * `web01` → Nginx

> Use **Git Bash** for SSH connections — CMD might not support them properly.

---
<img width="1920" height="1080" alt="Screenshot (681)" src="https://github.com/user-attachments/assets/71466664-f44e-4769-a390-7cbf35bb93fd" />
<img width="1920" height="1080" alt="Screenshot (679)" src="https://github.com/user-attachments/assets/86018218-421f-4cdf-8b7f-6ed01b255c4c" />

## Step 5: Verify VM Status

```bash
vagrant status
```

All 5 VMs should be listed as **running**.

---
<img width="1920" height="1080" alt="Screenshot (658)" src="https://github.com/user-attachments/assets/0eac5e2a-6b57-45ed-8f16-e1205484bba8" />

## Step 6: Access VMs via SSH

Examples:

```bash
vagrant ssh db01   # MySQL
vagrant ssh mc01   # Memcache
vagrant ssh rmq01  # RabbitMQ
vagrant ssh app01  # Tomcat
vagrant ssh web01  # Nginx
```

> If SSH fails, open **VirtualBox GUI → Right-click VM → Show → use the terminal** inside the VM.

---
<img width="1920" height="1080" alt="Screenshot (659)" src="https://github.com/user-attachments/assets/fae95b91-b22f-4333-9c79-d60a8b114100" />

## Step 7: Configure Each Service

### MySQL (db01)

```bash
sudo dnf update -y
sudo dnf install -y mariadb-server mariadb
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo mysql_secure_installation
```

**Root Password:** `admin`

Then:

```bash
mysql -u root -padmin
create database accounts;
grant all privileges on accounts.* to 'admin'@'localhost' identified by 'admin';
grant all privileges on accounts.* to 'admin'@'%' identified by 'admin';
flush privileges;
exit
```
<img width="1920" height="1080" alt="Screenshot (665)" src="https://github.com/user-attachments/assets/4a300ac0-989e-4c2e-ae55-af74eb69b33b" />

**Restore Database Dump**

```bash
nano db_backup.sql  # Paste SQL content here
mysql -u root -padmin accounts < db_backup.sql
```
<img width="1920" height="1080" alt="Screenshot (676)" src="https://github.com/user-attachments/assets/07af125d-2f7c-4171-87b0-6cbb0806f35f" />

**Configure Firewall**

```bash
sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo firewall-cmd --zone=public --add-port=3306/tcp --permanent
sudo firewall-cmd --reload
sudo systemctl restart mariadb
```

---
<img width="1920" height="1080" alt="Screenshot (677)" src="https://github.com/user-attachments/assets/cae72aa1-55cb-4810-9f46-3e68d27ff6d8" />

### Memcache (mc01)

```bash
sudo dnf update -y
sudo dnf install epel-release -y
sudo dnf install memcached -y
sudo systemctl start memcached
sudo systemctl enable memcached
sudo sed -i 's/127.0.0.1/0.0.0.0/g' /etc/sysconfig/memcached
sudo systemctl restart memcached
sudo firewall-cmd --add-port=11211/tcp --permanent
sudo firewall-cmd --add-port=11111/udp --permanent
sudo firewall-cmd --reload
```

---
<img width="1920" height="1080" alt="Screenshot (680)" src="https://github.com/user-attachments/assets/73477be4-4888-4659-934b-3bd54bbe963f" />
<img width="1920" height="1080" alt="Screenshot (682)" src="https://github.com/user-attachments/assets/164d80c8-4b6f-43c7-9f9b-babc03f29d7c" />

### RabbitMQ (rmq01)

```bash
sudo dnf update -y
sudo dnf install epel-release -y
sudo dnf -y install centos-release-rabbitmq-38
sudo dnf --enablerepo=centos-rabbitmq-38 -y install rabbitmq-server
sudo systemctl enable --now rabbitmq-server
echo "loopback_users = none" | sudo tee /etc/rabbitmq/rabbitmq.conf
sudo rabbitmqctl add_user test test
sudo rabbitmqctl set_user_tags test administrator
sudo rabbitmqctl set_permissions -p / test ".*" ".*" ".*"
sudo systemctl restart rabbitmq-server
```

---

### Tomcat (app01)

```bash
sudo dnf update -y
sudo dnf install java-11-openjdk java-11-openjdk-devel git maven wget -y
java --version
mvn --version
wget https://archive.apache.org/dist/tomcat/tomcat-9/v9.0.75/bin/apache-tomcat-9.0.75.tar.gz
tar xzvf apache-tomcat-9.0.75.tar.gz
sudo useradd --home-dir /usr/local/tomcat --shell /sbin/nologin tomcat
sudo cp -r apache-tomcat-9.0.75/* /usr/local/tomcat/
sudo chown -R tomcat.tomcat /usr/local/tomcat
```
<img width="1920" height="1080" alt="Screenshot (683)" src="https://github.com/user-attachments/assets/0e873097-48e8-43ac-a4f2-b5cb9fe7b0c3" />
<img width="1920" height="1080" alt="Screenshot (684)" src="https://github.com/user-attachments/assets/f27ddb8f-5094-42f6-81b4-022865a00efe" />
<img width="866" height="476" alt="Screenshot 2025-10-25 203103" src="https://github.com/user-attachments/assets/dc86b2f7-14de-4348-9e5d-94aee316155c" />

**Create Systemd Service**

```bash
sudo nano /etc/systemd/system/tomcat.service
```

*Paste this content:*

```ini
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
User=tomcat
Group=tomcat
Type=forking
Environment=JAVA_HOME=/usr/lib/jvm/jre
Environment=CATALINA_HOME=/usr/local/tomcat
ExecStart=/usr/local/tomcat/bin/startup.sh
ExecStop=/usr/local/tomcat/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```

Then:

```bash
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat
sudo firewall-cmd --add-port=8080/tcp --permanent
sudo firewall-cmd --reload
```

**Deploy WAR File**

```bash
cd /tmp
git clone https://github.com/Muncodex/digiprofile-project.git
cd digiprofile-project
git checkout local-setup-mun
mvn clean package
sudo rm -rf /usr/local/tomcat/webapps/ROOT
sudo cp target/digiprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
sudo chown -R tomcat.tomcat /usr/local/tomcat/webapps
```

**Check Application:**
👉 [http://192.168.56.12:8080](http://192.168.56.12:8080)

---

### Nginx (web01)

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install nginx -y
```

**Configure Reverse Proxy**

```bash
sudo nano /etc/nginx/sites-available/digiprofileapp
```

*Paste:*

```nginx
upstream digiprofileapp {
  server app01:8080;
}
server {
  listen 80;
  location / {
    proxy_pass http://digiprofileapp;
  }
}
```

Enable and restart Nginx:

```bash
sudo rm -rf /etc/nginx/sites-enabled/default
sudo ln -s /etc/nginx/sites-available/digiprofileapp /etc/nginx/sites-enabled/digiprofileapp
sudo systemctl restart nginx
```
<img width="870" height="255" alt="Screenshot 2025-10-25 203028" src="https://github.com/user-attachments/assets/0cb653b5-2334-4f07-baee-9a920ce11e46" />

<img width="866" height="457" alt="Screenshot 2025-10-25 203045" src="https://github.com/user-attachments/assets/2fa72b2b-0513-4fc8-8191-03bf60b45810" />

**Access Application:**
👉 [http://192.168.56.12](http://192.168.56.12)
**Username:** `nextgen7`
**Password:** `nextgen7`

---

## Final Output

1. All 5 VMs up and configured
2. Database, Cache, Message Queue, App Server, and Web Layer connected
3. Java application deployed successfully on Tomcat
4. Accessible via Nginx reverse proxy

---

## Troubleshooting Tips

* If `vagrant up` fails → run `vagrant destroy -f && vagrant up`
* If SSH issues occur → use VirtualBox GUI terminal.
* If ports conflict → modify private IPs in Vagrantfile.

---


