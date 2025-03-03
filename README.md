---

#                       **Deploying a React App on AWS Elastic Beanstalk with a Private EC2 and ALB**

## **Project Overview**
This project demonstrates how to deploy a React application using AWS Elastic Beanstalk while keeping the EC2 instance private and using an **Application Load Balancer (ALB)** for handling traffic.

---

## **Steps to Deploy the React App**

### **1. Clone the Repository**
```bash
git clone https://github.com/ritikops/react-node-project.git
cd react-node-project
```

---

### **2. Unzip the Project (If using deploy.zip)**
If you downloaded the `deploy.zip` file from this repository, extract it first:
```bash
unzip deploy.zip
cd deploy
```

---

### **3. Install Dependencies (Optional)**
Ensure you have **Node.js** installed, then install project dependencies:
```bash
npm install
```

---

### **4. Build the React Application**
```bash
npm run build
```
This generates the production-ready files inside the `build/` directory.

---

## **5. Set Up AWS Elastic Beanstalk**

### **5.1 Create an Elastic Beanstalk Application**
1. Open the **AWS Management Console**.
2. Navigate to **Elastic Beanstalk**.
3. Click **Create Application**.
4. Enter:
   - **Application Name**: `React-App-Using-ElasticBeanstalk`
   - **Platform**: **Node.js** (React runs on Node.js server for deployment).
   - **Application Code**: Choose **Upload your code** and upload `deploy.zip`.

---

### **5.2 Configure the Environment**
1. Click **"Configure more options"** before creating the environment.
2. Under **Capacity**, ensure **Load Balancer** is enabled.
3. Click **Edit** next to **Load Balancer** and set it up as follows:

---

## **6. Create and Configure the Application Load Balancer (ALB)**

### **6.1 Create the ALB in AWS Console**
1. **Go to AWS Console** → Open **EC2**.
2. In the left menu, click **Load Balancers**.
3. Click **Create Load Balancer**.
4. **Select "Application Load Balancer"**.
5. Configure as follows:
   - **Name**: `react-app-alb`
   - **Scheme**: **Internet-facing**
   - **IP Type**: IPv4
   - **VPC**: Choose an **existing VPC** where EC2 instances will be private.
   - **Availability Zones**: Select **public subnets** (ALB must be public).
6. **Listeners and Routing**:
   - Create a listener for **HTTP (80)** → Forward traffic to a **Target Group**.
7. Click **Next** and then **Create ALB**.

---

### **6.2 Configure Target Group for ALB**
1. **Go to EC2 Dashboard** → Click **Target Groups** (left menu).
2. Click **Create Target Group**.
3. **Choose**:
   - **Target Type**: **Instances**
   - **Protocol**: HTTP (80)
   - **VPC**: Same as the ALB
4. Click **Next** and **Register Targets**:
   - Select your private EC2 instances **launched by Elastic Beanstalk**.
   - Click **Include as Pending Below** → Click **Create Target Group**.
5. Now, go back to your **ALB settings** → Under **Listeners**, attach the newly created **Target Group**.

---

### **6.3 Configure Security Groups for ALB & EC2**
1. **ALB Security Group**:
   - Allow **HTTP (80) from anywhere (0.0.0.0/0)**
2. **EC2 Security Group**:
   - Allow **only traffic from ALB** (Do not allow direct internet access).

---

## **7. Deploy the React App to Elastic Beanstalk**
1. Go to **Elastic Beanstalk Console** → Click **Environments**.
2. Select your **React-App-Using-ElasticBeanstalk** environment.
3. Click **Upload and Deploy** → Choose **deploy.zip**.
4. Wait for deployment to complete.

---

## **8. Configure IAM Roles for Elastic Beanstalk and EC2**

### **8.1 Create an IAM Role for Elastic Beanstalk**
1. **Go to AWS IAM Console** → Click **Roles**.
2. Click **Create Role** → Select **AWS Service** → Choose **Elastic Beanstalk**.
3. Attach these policies:
   - `AWSElasticBeanstalkFullAccess`
   - `AmazonS3FullAccess` (if storing logs in S3)
   - `CloudWatchFullAccess` (for monitoring)
4. Attach this role to your **Elastic Beanstalk environment**.

---

### **8.2 Create an IAM Role for EC2**
1. **Go to IAM Console** → Click **Roles** → **Create Role**.
2. Choose **AWS Service** → Select **EC2**.
3. Attach these policies:
   - `AmazonEC2FullAccess`
   - `AmazonSSMFullAccess`
   - `CloudWatchAgentServerPolicy`
4. Attach this role to the **EC2 instance running inside Elastic Beanstalk**.

---

## **9. Set Up CloudWatch Alarms for Monitoring**
1. Go to **AWS Console → CloudWatch**.
2. Click **Alarms** → **Create Alarm**.
3. Set up alarms for:
   - **High CPU Utilization** (`> 80% for 5 minutes`).
   - **HTTP 5XX Errors on ALB** (indicating app issues).
   - **Instance Status Check Failures** (if EC2 is down).
4. Attach **SNS Notifications** to receive alerts.

---

## **10. Test the Deployment**
1. Get the **Load Balancer URL** from AWS Console.
2. Open a browser and enter:
   ```
   http://your-alb-url.amazonaws.com
   ```
3. If everything is set up correctly, your **React app should be live!**

---

## **11. Troubleshooting**
- **503 Service Unavailable?**
  - Check if **ALB Target Group** has registered EC2 instances.
  - Verify that **EC2 instances are healthy**.
  - Run `curl -I http://localhost:80` from EC2 to test if the app is running.
- **React App Not Loading?**
  - Check **Elastic Beanstalk logs** in AWS Console.
  - Run `eb logs` (if CLI is installed).
- **EC2 Private Instance Not Responding?**
  - Check **NAT Gateway configuration** in your VPC.

---

## **Conclusion**
This guide provides a **step-by-step** approach to deploying a React app on AWS **Elastic Beanstalk** with a **private EC2 instance and an ALB**. It ensures **scalability, security, and monitoring** using CloudWatch.

---

This is a complete guide with detailed **ALB setup steps**. 🚀
