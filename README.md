# 🚀 Deploy Your Node.js Project on AWS EC2

A complete step-by-step guide to deploy your Node.js application on AWS EC2 instance from scratch. Perfect for beginners!

## 📋 Table of Contents
- [Prerequisites](#prerequisites)
- [Step 1: Prepare Your Project](#step-1-prepare-your-project)
- [Step 2: Setup IAM User](#step-2-setup-iam-user)
- [Step 3: Launch EC2 Instance](#step-3-launch-ec2-instance)
- [Step 4: Connect via SSH](#step-4-connect-via-ssh)
- [Step 5: Configure Server Environment](#step-5-configure-server-environment)
- [Step 6: Deploy Your Application](#step-6-deploy-your-application)
- [Step 7: Configure Security Groups](#step-7-configure-security-groups)
- [Troubleshooting](#troubleshooting)

---

## Prerequisites

Before you begin, make sure you have:
- A Node.js project ready to deploy
- An AWS account
- Basic knowledge of terminal/command line
- Your project pushed to GitHub

---

## Step 1: Prepare Your Project

### 1.1 Setup Your Node.js Project
Create a simple Node.js application with the following structure:

```
your-project/
├── index.js
├── package.json
├── .env
├── .gitignore
└── README.md
```

### 1.2 Configure Environment Variables
Create a `.env` file for your environment variables:

```env
PORT=3000
NODE_ENV=production
```

### 1.3 Add .gitignore
**Important:** Always gitignore your `.env` file:

```gitignore
node_modules/
.env
*.pem
```

### 1.4 Push to GitHub
```bash
git init
git add .
git commit -m "Initial commit"
git remote add origin <your-repo-url>
git push -u origin main
```

---

## Step 2: Setup IAM User

### Why Use IAM User Instead of Root?
🛡️ **Security Best Practices:**
- Root account has unrestricted access to all AWS resources
- IAM users operate within defined permissions and guardrails
- Enables better access control and audit trails
- Reduces risk of accidental or malicious actions
- Follows the principle of least privilege

### 2.1 Create IAM User
1. Log into AWS Console as root user
2. Navigate to **IAM** (Identity and Access Management)
3. Click **Users** → **Add User**
4. Enter username (e.g., `ec2-deployer`)
5. Select **AWS Management Console access**
6. Set a password and configure password reset requirements
7. Click **Next: Permissions**

### 2.2 Attach Policies
Attach the following policies:
- `AmazonEC2FullAccess`
- (Add other policies as needed for your project)

### 2.3 Complete Setup
1. Review and create user
2. Download credentials (save them securely!)
3. **Log out from root account**
4. **Log in as your new IAM user**

---

## Step 3: Launch EC2 Instance

### 3.1 Navigate to EC2 Dashboard
- Go to AWS Console → EC2
- Click **Launch Instance**

### 3.2 Configure Instance
1. **Name your instance:** `my-nodejs-server`

2. **Choose AMI (Operating System):**
   - Select **Ubuntu Server 22.04 LTS** (recommended)
   - Architecture: 64-bit (x86)

3. **Choose Instance Type:**
   - Select **t2.micro** (Free tier eligible)

4. **Key Pair (CRITICAL):**
   - Click **Create new key pair**
   - Name: `demo.pem` (or any name you prefer)
   - Key pair type: RSA
   - Private key file format: `.pem`
   - **Download and save securely** - you can't download it again!

5. **Network Settings:**
   - ✅ Allow SSH traffic from Anywhere (0.0.0.0/0)
   - ✅ Allow HTTPS traffic from the internet
   - ✅ Allow HTTP traffic from the internet

6. **Configure Storage:**
   - Keep default (8 GB is usually sufficient for small projects)

7. **Launch Instance:**
   - Click **Launch Instance**
   - Wait for instance state to show **Running** ✅

---

## Step 4: Connect via SSH

### 4.1 Locate Your Key Pair
Move your downloaded `.pem` file to a secure location:

```bash
# Example: Move to ~/.ssh directory
mv ~/Downloads/demo.pem ~/.ssh/
```

### 4.2 Set Correct Permissions
This command gives read-only access to the key file (required for SSH):

```bash
chmod 400 ~/.ssh/demo.pem
```

**What does `chmod 400` do?**
- `4` = Read permission for owner
- `0` = No permissions for group
- `0` = No permissions for others

### 4.3 Get Your Instance IP
1. Go to EC2 Dashboard
2. Select your instance
3. Copy the **Public IPv4 address** (e.g., `54.123.45.67`)

### 4.4 Connect via SSH
Run this command (replace with your details):

```bash
ssh -i ~/.ssh/demo.pem ubuntu@YOUR_INSTANCE_IP
```

Example:
```bash
ssh -i ~/.ssh/demo.pem ubuntu@54.123.45.67
```

### 4.5 Accept Connection
You'll see a message like:
```
The authenticity of host '54.123.45.67' can't be established.
Are you sure you want to continue connecting (yes/no)?
```

Type `yes` and press Enter.

🎉 **You're now connected to your Ubuntu server!**

---

## Step 5: Configure Server Environment

### 5.1 Update System Packages
```bash
sudo apt update
sudo apt upgrade -y
```

### 5.2 Install Git
Check if Git is installed:
```bash
git --version
```

If not installed, follow [Digital Ocean's Guide](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-22-04):

```bash
sudo apt install git -y
git --version
```

Configure Git (optional but recommended):
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### 5.3 Install Node.js and npm
Follow [Digital Ocean's Node.js Guide](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04):

#### Option 1: Using NodeSource PPA (Recommended)
```bash
# Install Node.js 20.x (LTS)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```

#### Option 2: Using apt (older version)
```bash
sudo apt install nodejs npm -y
```

#### Verify Installation
```bash
node --version
npm --version
```

---

## Step 6: Deploy Your Application

### 6.1 Clone Your Repository
```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO.git
cd YOUR_REPO
```

### 6.2 Create Environment File
Since `.env` was gitignored, create it manually:

```bash
touch .env
```

View all files (including hidden ones):
```bash
ls -a
```

### 6.3 Edit .env File
Use vim to edit the file:

```bash
vim .env
```

**Vim Quick Guide:**
- Press `i` to enter INSERT mode
- Paste your environment variables:
  ```
  PORT=3000
  NODE_ENV=production
  ```
- Press `ESC` to exit INSERT mode
- Type `:wq` and press Enter to save and quit

Verify your .env file:
```bash
cat .env
```

### 6.4 Install Dependencies
```bash
npm install
```

### 6.5 Test Your Application
```bash
npm start
# or
node index.js
```

---

## Step 7: Configure Security Groups

### 🚨 Why Your App Isn't Accessible Yet
By default, EC2 instances only expose port 22 (SSH). Your application port (e.g., 3000) needs to be explicitly opened in the security group.

### 7.1 Open Required Port
1. Go to **EC2 Dashboard**
2. Select your instance
3. Click on **Security** tab
4. Click on the **Security Group** link (e.g., `sg-xxxxx`)
5. Click **Edit inbound rules**
6. Click **Add rule**
7. Configure:
   - **Type:** Custom TCP
   - **Port range:** `3000` (or your app's port)
   - **Source:** `0.0.0.0/0` (Anywhere IPv4)
   - **Description:** Node.js application port
8. Click **Save rules**

### 7.2 Access Your Application
Open your browser and navigate to:

```
http://YOUR_INSTANCE_IP:3000
```

Example:
```
http://54.123.45.67:3000
```

🎉 **Your application is now LIVE on AWS!**

---

## Troubleshooting

### Application Not Accessible
- ✅ Check security group has port 3000 open
- ✅ Verify app is running: `npm start`
- ✅ Check firewall: `sudo ufw status`
- ✅ Verify correct IP address and port

### SSH Connection Failed
- ✅ Verify `.pem` file permissions: `chmod 400 demo.pem`
- ✅ Check security group allows SSH (port 22)
- ✅ Verify using correct username (`ubuntu` for Ubuntu AMI)
- ✅ Confirm instance is running

### npm install Fails
- ✅ Check Node.js is installed: `node --version`
- ✅ Try: `npm cache clean --force`
- ✅ Delete `node_modules` and `package-lock.json`, then retry

---

## 🎯 Production Tips

### Keep Application Running
Use PM2 to keep your app running even after closing SSH:

```bash
sudo npm install -g pm2
pm2 start index.js --name "my-app"
pm2 startup
pm2 save
```

### Setup Nginx as Reverse Proxy
```bash
sudo apt install nginx -y
# Configure nginx to proxy port 80 to your app port
```

### Enable HTTPS with Let's Encrypt
```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com
```

---

## 🎓 Congratulations!

You've successfully deployed your first Node.js application on AWS EC2! 

**What you learned:**
- ✅ AWS IAM user setup and best practices
- ✅ EC2 instance creation and configuration
- ✅ SSH connection and key management
- ✅ Linux server management basics
- ✅ Git, Node.js, and npm installation
- ✅ Security group configuration
- ✅ Application deployment workflow

---

## 📚 Additional Resources

- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)
- [PM2 Documentation](https://pm2.keymetrics.io/)
- [Digital Ocean Tutorials](https://www.digitalocean.com/community/tutorials)

---

## 📝 License

MIT License - Feel free to use this guide for your projects!

---

**Made with ❤️ for developers learning AWS deployment**
