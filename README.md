# AWS Multi-Architecture C Application Deployment

## 📌 Project Overview

This project demonstrates the deployment of a C application across multiple CPU architectures using AWS EC2, Docker, Amazon ECR, Amazon S3, and IAM.

The same C application is compiled and tested on:

- x86_64 architecture using an EC2 t3.micro instance
- ARM64 architecture using an EC2 t4g.micro Graviton instance

The project demonstrates architecture-specific compilation, Docker containerization, Amazon ECR image management, Amazon S3 artifact storage, and secure EC2-to-S3 communication using IAM roles.

---

## 🏗️ Architecture

                         AWS Cloud
                            │
              ┌─────────────┴─────────────┐
              │                           │
        Amazon ECR                   Amazon S3
     c-multiarch-app          my-c-app-artifacts-2026
              │                           │
        Docker Image                Application Artifact
              │
        ┌─────┴─────┐
        │           │
    EC2 x86_64    EC2 ARM64
    c-app-x86    c-app-gravitation
        │           │
       GCC         GCC
        │           │
    C-Application  C-Application
        │           │
     x86-64       ARM64
     Binary       Binary

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| AWS EC2 | Compute instances for different CPU architectures |
| Amazon ECR | Docker image registry |
| Amazon S3 | Application artifact storage |
| AWS IAM | Secure EC2-to-S3 access |
| Docker | Application containerization |
| GCC | C application compilation |
| Amazon Linux 2023 | Operating system |
| AWS CLI | AWS resource management |

---

## 🖥️ EC2 Instances

### x86_64 Instance

- Name: `c-app-x86`
- Instance Type: `t3.micro`
- Architecture: `x86_64`

Architecture verification:

    uname -m

Output:

    x86_64

### ARM64 Instance

- Name: `c-app-graviton`
- Instance Type: `t4g.micro`
- Architecture: `ARM64 / aarch64`

Architecture verification:

    uname -m

Output:

    aarch64

---

## 🔐 Security Group Configuration

An EC2 security group was configured to allow SSH access.

- Protocol: TCP
- Port: 22
- Type: SSH
- Source: Restricted to the required IP address

This provides controlled SSH access to the EC2 instances instead of exposing SSH access to the entire internet.

---

## ⚙️ C Application

A simple C application was created to demonstrate compilation and execution on different CPU architectures.

Example output on x86_64:

    Hello from AWS Multi-Architecture Application!
    Architecture: x86_64

Example output on ARM64:

    Hello from AWS Multi-Architecture Application!
    Architecture: ARM64

The generated executable was verified using:

    file app

The x86_64 instance produced an x86-64 ELF executable, while the ARM64 instance produced an ARM aarch64 ELF executable.

---

## 🔧 GCC Compilation

GCC was installed on both EC2 instances.

Installation:

    sudo dnf install -y gcc

Verify GCC:

    gcc --version

Compile the application:

    gcc main.c -o app

Run the application:

    ./app

Verify the binary architecture:

    file app

This confirms that the same C source code can be compiled into architecture-specific binaries.

---

## 🐳 Docker Setup

Docker was installed and enabled on both EC2 instances.

Install Docker:

    sudo dnf install -y docker

Enable and start Docker:

    sudo systemctl enable --now docker

Add the EC2 user to the Docker group:

    sudo usermod -aG docker ec2-user

Verify Docker:

    sudo docker --version

---

## 📦 Docker Image

A Dockerfile was created using GCC as the base image.

Build the Docker image:

    sudo docker build -t c-multiarch-app .

Verify the image:

    sudo docker images

The resulting image is:

    c-multiarch-app:latest

---

## ☁️ Amazon ECR

A private Amazon ECR repository was created:

    c-multiarch-app

The repository is used to store the Docker image generated for the application.

Authenticate Docker with Amazon ECR:

    aws ecr get-login-password --region us-east-1 | sudo docker login --username AWS --password-stdin <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com

Tag the Docker image:

    sudo docker tag c-multiarch-app:latest <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/c-multiarch-app:latest

Push the image:

    sudo docker push <ACCOUNT_ID>.dkr.ecr.us-east-1.amazonaws.com/c-multiarch-app:latest

---

## 🪣 Amazon S3 Artifact Storage

An S3 bucket was created for storing application artifacts:

    my-c-app-artifacts-2026

List the bucket:

    aws s3 ls s3://my-c-app-artifacts-2026

Upload the compiled application:

    aws s3 cp ./app s3://my-c-app-artifacts-2026/app-x86

The application binary is therefore stored centrally in Amazon S3.

---

## 🔑 IAM Role

An IAM role named:

    EC2-S3-CApp-Role

was created with EC2 as the trusted entity.

The role allows the EC2 instance to access Amazon S3 without storing AWS access keys directly on the server.

Verify the currently assumed IAM role:

    aws sts get-caller-identity

This provides a secure method for EC2 instances to interact with AWS services.

---

## 🔄 Project Workflow

    1. Create Amazon ECR Private Repository
                    ↓
    2. Create Amazon S3 Artifact Bucket
                    ↓
    3. Launch x86_64 EC2 Instance
                    ↓
    4. Launch ARM64/Graviton EC2 Instance
                    ↓
    5. Configure EC2 Security Group
                    ↓
    6. Verify CPU Architectures
                    ↓
    7. Install GCC
                    ↓
    8. Compile the C Application
                    ↓
    9. Verify Architecture-Specific Binaries
                    ↓
    10. Install Docker
                    ↓
    11. Create Dockerfile
                    ↓
    12. Build Docker Image
                    ↓
    13. Create IAM Role
                    ↓
    14. Grant EC2 Access to S3
                    ↓
    15. Upload Application Artifact to S3
                    ↓
    16. Authenticate with Amazon ECR
                    ↓
    17. Tag Docker Image
                    ↓
    18. Push Docker Image to ECR

---

## 📁 AWS Resources

### Amazon EC2

- `c-app-x86`
- `c-app-graviton`

### Amazon ECR

- Repository: `c-multiarch-app`

### Amazon S3

- Bucket: `my-c-app-artifacts-2026`

### AWS IAM

- Role: `EC2-S3-CApp-Role`

---

## 🎯 Objectives

- Understand x86_64 and ARM64 CPU architectures.
- Deploy the same application on different EC2 architectures.
- Compile C applications for different architectures.
- Verify executable architecture using Linux tools.
- Containerize the application using Docker.
- Store Docker images in Amazon ECR.
- Store application artifacts in Amazon S3.
- Configure secure EC2-to-S3 access using IAM roles.
- Understand the fundamentals of multi-architecture cloud deployment.

---

## 📊 Architecture Comparison

| Feature | x86_64 | ARM64 |
|---|---|---|
| EC2 Instance | c-app-x86 | c-app-graviton |
| Instance Type | t3.micro | t4g.micro |
| Architecture | x86_64 | aarch64 |
| Compiler | GCC | GCC |
| Output Binary | x86-64 ELF | ARM64 ELF |
| Operating System | Amazon Linux 2023 | Amazon Linux 2023 |

---

## 🧪 Verification

The following commands were used during the deployment to verify the environment:

    uname -m

    gcc --version

    gcc main.c -o app

    ./app

    file app

    sudo docker --version

    sudo docker images

    aws sts get-caller-identity

    aws s3 ls s3://my-c-app-artifacts-2026

These commands verify the CPU architecture, compiler, application execution, binary architecture, Docker installation, IAM identity, and S3 connectivity.

---

## ✅ Result

The C application was successfully compiled and executed on both x86_64 and ARM64 EC2 instances.

The resulting binaries were verified to match their respective CPU architectures.

Docker was successfully installed and configured, and the application was containerized into the `c-multiarch-app` image.

An Amazon ECR repository was created to store the Docker image, while Amazon S3 was used to store the compiled application artifact.

An IAM role was configured to provide secure EC2-to-S3 access without requiring hard-coded AWS credentials.

Overall, the project successfully demonstrates the fundamentals of deploying a C application across multiple CPU architectures using AWS EC2, Docker, Amazon ECR, Amazon S3, and IAM.

---

## 👨‍💻 Project Summary

This project provides a practical demonstration of **multi-architecture application deployment on AWS**, covering the complete workflow from compiling a C application on different CPU architectures to containerization, artifact storage, IAM-based access, and Docker image management using Amazon ECR.
