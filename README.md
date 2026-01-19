# WordPress Deployment on AWS with Docker, Nginx, and Terraform

[![Terraform](https://img.shields.io/badge/Terraform-1.0+-623CE4.svg)](https://www.terraform.io/)
[![AWS](https://img.shields.io/badge/AWS-Cloud-FF9900.svg)](https://aws.amazon.com/)
[![Docker](https://img.shields.io/badge/Docker-2496ED.svg)](https://www.docker.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

This project automates the setup of a scalable and highly available WordPress deployment on AWS using Docker containers orchestrated with Docker Compose, Nginx as a load balancer, and Terraform for infrastructure provisioning.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Features](#features)
- [Architecture](#architecture)
- [Requirements](#requirements)
- [Expected Output](#expected-output)
- [Setup Instructions](#setup-instructions)
- [Usage](#usage)
- [Scripts Explanation](#scripts-explanation)
- [Contributing](#contributing)
- [Author](#author)
- [License](#license)

## Prerequisites

- [Terraform](https://www.terraform.io/downloads.html) (v1.0 or later)
- [AWS CLI](https://aws.amazon.com/cli/) configured with appropriate permissions
- An AWS account with necessary IAM roles
- Git for cloning repositories
- SSH key pair for EC2 access

## Features

- **Automated Infrastructure**: Fully automated deployment using Terraform
- **Scalable WordPress**: Multi-container setup with load balancing
- **High Availability**: Auto Scaling Group for fault tolerance
- **Persistent Storage**: Ensures WordPress uploads are retained
- **Secure Networking**: Private subnets for database, public for load balancer

## Architecture

![Architecture Diagram](screenshot/ChatGPT%20Image%20Jan%2019%2C%202026%2C%2004_40_43%20PM.png)


```

Architecture
User
 ↓
Internet Gateway
 ↓
Nginx Load Balancer (EC2)
 ↓
Docker Compose
 ├── WordPress Container #1 (Port 8091)
 └── WordPress Container #2 (Port 8082)
 ↓
MySQL Database (EC2 – Private Subnet)
```

## Requirements

1. **Create an EC2 Machine and Install Docker**
2. **Create a Docker Compose File with the Following Requirements:**
   - Use the WordPress image.
   - Run 2 containers of WordPress on 2 different ports consecutively (the 1st instance must be up and healthy before starting the 2nd instance).
   - Use a MySQL database on a different EC2 machine.
3. **Create an Nginx Load Balancer to Balance Traffic Between the 2 WordPress Instances**
4. **Create an AMI from the EC2 Machine and Add it to Your Launch Template**
5. **Ensure that `wp-content/uploads` Directory is Persistent**
6. **Add 1 Machine to Your Auto Scaling Group**

## Expected Output

A machine in your auto-scaling group with Nginx forwarding traffic to 2 WordPress containers that are linked to the MySQL database. The files in `wp-content/uploads` should always be present when containers get replaced.

## Setup Instructions

### 1. Create EC2 Instances

1. **Network Setup**:
   - Configure VPC, subnets, and security groups using the provided Terraform module `network`.

2. **MySQL Database EC2**:
   - Launch an EC2 instance for the MySQL database using the provided Terraform module `frontendEC2`.
   - Install MySQL and set up the WordPress database using the `mysql_installation.sh` script.

### 2. Setup MySQL Database

- Ensure MySQL is running and configured to allow remote connections.
- Note down the private IP address of the MySQL instance.

### 3. Configure Docker and Docker Compose

1. **Docker Installation**:
   - Install Docker and Docker Compose on the EC2 instance using the `TEMPLATE_installation.sh` script.

2. **Update Docker Compose**:
   - Use the `update_docker_compose.sh` script to dynamically update the Docker Compose file with the MySQL database IP.

### 4. Create AMI and Launch Template

- Create an AMI from the EC2 instance and add it to your launch template using the provided Terraform module `launch_template`.

### 5. Setup Auto Scaling Group

- Set up an Auto Scaling Group with the created launch template to ensure high availability and scalability using the provided Terraform module `ASG`.

## Quick Start

1. **Clone the repository**:
   ```bash
   git clone https://github.com/Belal2015/terraform_mysql_wordpress.git
   cd terraform_mysql_wordpress
   ```

2. **Initialize Terraform**:
   ```bash
   terraform init
   ```

3. **Review the plan**:
   ```bash
   terraform plan -var="access_key=YOUR_ACCESS_KEY" -var="secret_key=YOUR_SECRET_KEY"
   ```

4. **Apply the configuration**:
   ```bash
   terraform apply -var="access_key=YOUR_ACCESS_KEY" -var="secret_key=YOUR_SECRET_KEY"
   ```

5. **Get the DNS link**:
   ```bash
   terraform output DNS_LINK
   ```

## Usage

- Access the WordPress site through the DNS link provided by the load balancer output in Terraform.
- Verify that the `wp-content/uploads` directory remains persistent across container replacements.

## Scripts Explanation

### `update_docker_compose1.sh`

The `update_docker_compose1.sh` script performs the following tasks:
1. **Clones the Repository**: It clones the GitHub repository containing the Docker Compose template.
2. **Fetches MySQL IP**: It fetches the private IP address of the MySQL instance from `instance_ip.txt`.
3. **Updates Docker Compose File**: It replaces a placeholder in the Docker Compose template with the actual MySQL IP address.
4. **Commits and Pushes Changes**: It commits the updated Docker Compose file to a new branch and pushes the changes to the GitHub repository.

This script ensures that the Docker Compose file always has the correct MySQL IP address, allowing the WordPress containers to connect to the database without manual intervention.

### Other Scripts

- `mysql_installation.sh`: Installs and configures MySQL on the database EC2 instance.
- `TEMPLATE_installation.sh`: Installs Docker and Docker Compose, then deploys WordPress containers.

## Contributing

To contribute to this project, please follow these steps:

1. Fork the repository.
2. Create a new branch (`git checkout -b feature/your-feature`).
3. Commit your changes (`git commit -m 'Add some feature'`).
4. Push to the branch (`git push origin feature/your-feature`).
5. Open a pull request.

## Author

👤 **Belal Mahmoud**  
🚀 DevOps Engineer

Passionate about building scalable, automated, and cloud-native infrastructures using AWS, Docker, Terraform, and CI/CD best practices.

🔗 Connect with me:

- 💻 GitHub: https://github.com/Belal2015
- 🔗 LinkedIn: https://www.linkedin.com/in/belal-mahmoud-devops/
- 📧 Email: belalmahmoud8183@gmail.com

## License

📜 This project is licensed under the MIT License.

You are free to:

- ✅ Use the project for personal or commercial purposes
- ✅ Modify and distribute the source code
- ✅ Integrate it into other projects

⚠️ Provided "as is", without warranty of any kind.
