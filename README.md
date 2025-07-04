# AWS EC2 Spring Boot Demo (Java 21)

This repository documents the full step-by-step process to deploy a simple **Spring Boot application** that returns **"Hello from EC2!"** on an **AWS EC2 instance** using **Java 21**.

---

## Technologies Used

| Item            | Details                     |
|-----------------|-----------------------------|
| Language / FW   | Java 21 / Spring Boot 3.5.3 |
| Infrastructure  | AWS EC2 (Amazon Linux 2023) |
| Build Tool      | Gradle                      |

---

## 1. EC2 Instance Setup

| Setting           | Value                                 |
|-------------------|----------------------------------------|
| Instance Name     | aws-ec2-springboot-demo                |
| Machine Image     | Amazon Linux 2023 AMI                  |
| Instance Type     | t3.micro                               |
| Key Pair Name     | aws-ec2-springboot-demo-key.pem        |
| Volume            | 8 GiB (gp3, root volume)               |
| Auto-assign IP    | Enabled                                |

### Inbound Rules

| Type        | Protocol | Port Range | Source     |
|-------------|----------|------------|------------|
| SSH         | TCP      | 22         | 0.0.0.0/0  |
| Custom TCP  | TCP      | 8080       | 0.0.0.0/0  |

---

## 2. Spring Boot App Setup (Local)

1. Create a new Spring Boot project
2. Example `HelloController`:

```java
@RestController
public class HelloController {
    @GetMapping("/")
    public String hello() {
        return "Hello from EC2!";
    }
}
```

### Build the JAR file

```bash
./gradlew bootJar
```

### Confirm the JAR is generated

```bash
ls build/libs/
# → aws-ec2-springboot-demo-0.0.1-SNAPSHOT.jar
```

---

## 3. Deploy to EC2

### Connect to EC2 via SSH

```bash
# Move to the folder where the .pem file is located
cd <path-to-pem-file>

# Secure the key file
chmod 400 aws-ec2-springboot-demo-key.pem

# Connect via SSH
ssh -i "aws-ec2-springboot-demo-key.pem" ec2-user@<your-ec2-public-dns>
```

### Install Java on EC2

```bash
# Update package manager
sudo yum update -y

# Install Amazon Corretto 21 (Java 21)
sudo yum install java-21-amazon-corretto-headless -y

# Confirm installation
java -version
```

### Transfer the JAR file to EC2

```bash
# Run from local terminal (not EC2)
scp -i aws-ec2-springboot-demo-key.pem \
build/libs/aws-ec2-springboot-demo-0.0.1-SNAPSHOT.jar \
ec2-user@<your-ec2-public-dns>:/home/ec2-user/
```

---

## 4. Run the App on EC2

```bash
# SSH into EC2 (if not already connected)
ssh -i "aws-ec2-springboot-demo-key.pem" ec2-user@<your-ec2-public-dns>

# Run the Spring Boot app
java -jar aws-ec2-springboot-demo-0.0.1-SNAPSHOT.jar
```

---

## 5. Confirm the Application is Running

Access the application in your browser:
```bash
http://<your-ec2-public-ip>:8080/
```

You should see the following response:
```bash
Hello from EC2!
```
<img width="706" alt="Hello from EC2！" src="https://github.com/user-attachments/assets/47d64c9d-6ab1-4e09-b962-6b79e6ee76a9" />

---



