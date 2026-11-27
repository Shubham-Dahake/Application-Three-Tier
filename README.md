# Three-Tier Web Application


## Project Overview

This project demonstrates the deployment of a **Three-Tier Web
Application on AWS** using a custom VPC, public and private subnets, EC2
instances, Nginx Reverse Proxy, Apache Tomcat 9, Java, and
MariaDB/Amazon RDS.

The Proxy Server receives browser requests, the Application Server runs
the Java web application, and the database layer stores student records.

The project covers AWS networking, private server access, NAT Gateway,
Internet Gateway, Elastic IP, reverse proxy configuration, application
deployment, and database connectivity.

## Architecture Diagram

<img width="1280" height="659" alt="Application" src="https://github.com/user-attachments/assets/0267a927-7218-4c41-9881-9f0cf779b777" />


## Technologies Used

-   **Cloud Platform:** Amazon Web Services (AWS)
-   **Networking:** VPC, Subnets, Route Tables
-   **Internet Connectivity:** Internet Gateway, NAT Gateway
-   **Public IP:** Elastic IP
-   **Compute:** Amazon EC2
-   **Server :** Nginx, Apache Tomcat 9
-   **Application:** Java-based Student Registration Application
-   **Database:** MariaDB / Amazon RDS
-   **Database Connectivity:**  MySQL Connector
-   **Operating System:** Linux
-   **Deployment:** WAR File
-   **Remote Access:** SSH

------------------------------------------------------------------------

## AWS Infrastructure Setup

### 1. Create a Custom VPC

Created a custom VPC named `three-tier-web-app` with the following
configuration:

-   **IPv4 CIDR:** `10.0.0.0/16`
-   **IPv6:** Not enabled

The VPC provides an isolated networking environment for the project
resources.

### 2. Create Subnets

Created three subnets across three Availability Zones.

**Purpose:**

-   Public Subnet: Hosts the Nginx Proxy Server.
-   Private Subnet 1: Hosts the Application Server.
-   Private Subnet 2: Hosts the DB Server.

### 3. Internet Gateway

Created an Internet Gateway and attached it to the custom VPC.

The Internet Gateway provides internet connectivity to resources in the
Public Subnet when public IP addressing and routing are configured
correctly.

### 4. NAT Gateway and Elastic IP

Created a NAT Gateway inside the Public Subnet and associated an Elastic
IP with it.

The NAT Gateway allows instances in the Private Subnets to initiate
outbound internet connections without requiring public IP addresses.

### 5. Route Tables

Created two route tables.

**Public Route Table (`public-rt`)**

-   Associated with the Public Subnet.
-   Contains the local VPC route.
-   Routes `0.0.0.0/0` to the Internet Gateway.

**Private Route Table (`private-rt`)**

-   Associated with both Private Subnets.
-   Contains the local VPC route.
-   Routes `0.0.0.0/0` to the NAT Gateway.

#### VPC and Subnet Resource Map

#### Internet Gateway and NAT Gateway Connections

<img width="1917" height="1078" alt="Screenshot 2026-11-27 121255" src="https://github.com/user-attachments/assets/a4d11a37-33e4-45f6-af00-a59a43b4e939" />


### 6. EC2 Instance Deployment

Launched three EC2 instances inside the custom VPC.
1.Proxy-server
2.App-server
3.database-server

The Proxy Server is publicly accessible. The private instances are
accessed through the Proxy Server using SSH jump/bastion access.

### 7. Security Groups

Configured Security Groups to control communication between the servers.

  | Port | Protocol | Service | Purpose |
|---|---|---|---|
| 22 | TCP | SSH | Remote server access |
| 80 | TCP | HTTP | Web application access |
| 3306 | TCP | MySQL / MariaDB | Database connectivity |
| 8080 | TCP | Tomcat | Application server communication |

Rules should be restricted to trusted IP addresses and the appropriate
source Security Groups. Avoid exposing SSH, Tomcat, or database ports to
the entire internet.

------------------------------------------------------------------------

## Server Configuration

### 8. Connect to EC2 Instances

Connected to the Proxy Server using SSH.

### 9. Proxy Server Setup -- Nginx

Installed Nginx on the Proxy Server and configured it as a Reverse
Proxy.

Nginx receives incoming HTTP requests and forwards them to the private
Application Server.

**Example Nginx configuration:**

``` nginx
location / {
    proxy_pass http://APP_SERVER_PRIVATE_IP:8080/student/;
}
```

**Restart Nginx:**

``` bash
sudo systemctl restart nginx
```

### 10. Application Server Setup -- Java and Tomcat

Connected to the private App Server through the Proxy Server.

Installed Java and configured Apache Tomcat 9.

Tomcat runs the Java-based Student Registration Application.

### 11. Download Student WAR File

Downloaded the Student Registration Application WAR file from GitHub.

**Command template:**

``` bash
curl -L -O <https://github.com/Shubham-Dahake/Application-Three-Tier/raw/refs/heads/main/student.war>
```

Copied `student.war` into Tomcat's `webapps` directory. Tomcat deploys
the application when it starts.

### 12. Download MySQL Connector

Downloaded the MySQL Connector JAR file from GitHub.

**Command template:**

``` bash
sudo wget -O mysql-connector.jar < https://github.com/Shubham-Dahake/Application-Three-Tier/raw/refs/heads/main/mysql-connector.jar>
```

Copied the connector JAR file into Tomcat's `lib` directory. The JDBC
driver allows the Java application to connect to a MariaDB/MySQL
database.

### 13. Tomcat Database Configuration

Configured Tomcat's database connection by editing:

``` bash
conf/context.xml
```

Configured the JDBC Resource with the database name, endpoint, username,
password, and JDBC driver according to the application requirements.


------------------------------------------------------------------------

## Database Configuration

### 14. Amazon RDS Setup

Created an Amazon RDS database using the MariaDB engine.

Configured:

-   MariaDB engine
-   Database instance
-   Database username and password
-   Database endpoint
-   Database connectivity

Configured connectivity between the Application Server and the RDS
database. The App Server uses the database endpoint for database
communication.

### 15. MariaDB Database and Table

Connected to MariaDB and used the `studentapp` database.

**Commands used:**

``` sql
SHOW DATABASES;
USE studentapp;
CREATE TABLE IF NOT EXISTS students (
    student_id INT NOT NULL AUTO_INCREMENT,
    student_name VARCHAR(100) NOT NULL,
    student_addr VARCHAR(100) NOT NULL,
    student_age VARCHAR(3) NOT NULL,
    student_qual VARCHAR(20) NOT NULL,
    student_percent VARCHAR(10) NOT NULL,
    student_year_passed VARCHAR(10) NOT NULL,
    PRIMARY KEY (student_id)
);
SHOW TABLES;
DESC students;
SELECT * FROM students;
```

#### Students Table Structure

 

The `student_id` column is the primary key and is configured with
auto-increment.

#### Database Table Structure


<img width="731" height="226" alt="Screenshot 2026-11-25 102621" src="https://github.com/user-attachments/assets/1bffe91c-b351-4ed5-9dc1-4d85a3fc7ae6" />

#### Student Records in MariaDB

<img width="1917" height="1077" alt="Screenshot 2026-11-27 122926" src="https://github.com/user-attachments/assets/f9c800cc-f35b-44b6-9558-01c3e13a1065" />

------------------------------------------------------------------------

## Application Deployment and Testing

### 16. Start Apache Tomcat

Connected to the Application Server and navigated to Tomcat's `bin`
directory.

**Command:**

``` bash
./catalina.sh start
```

Started Apache Tomcat to deploy and run the Student Registration
Application.

#### Tomcat Server Screenshot

<img width="622" height="350" alt="Screenshot 2026-11-25 202109" src="https://github.com/user-attachments/assets/4686fb8f-44ca-414e-b83e-f5e837e55992" />


### 17. Restart Nginx

After configuring the Reverse Proxy, restarted Nginx on the Proxy
Server.

**Command:**

``` bash
sudo systemctl restart nginx
```

Nginx forwards browser requests to the private Application Server on
port 8080.

### 18. Application Testing

Opened the Public IP address of the Proxy Server in a web browser.

Successfully accessed the Student Registration Application through the
Nginx Reverse Proxy.

**Application features:**

-   Student Registration
-   View Students List
-   Edit Student Details
-   Delete Student Details

#### Student Registration Form

<img width="1917" height="1078" alt="Screenshot 2026-11-27 115217" src="https://github.com/user-attachments/assets/da9f1b62-b6ec-401a-9d38-fffb0a22f45e" />



#### Students List

<img width="1917" height="1078" alt="Screenshot 2026-11-27 115209" src="https://github.com/user-attachments/assets/539b41e5-914a-4401-bee5-4b8ca1f898aa" />




------------------------------------------------------------------------



## Conclusion

This project provided hands-on experience deploying a Three-Tier Web
Application using AWS infrastructure. By combining VPC networking, EC2
instances, Nginx, Apache Tomcat, Java, and MariaDB/RDS, I practised
cloud networking, Linux server administration, application deployment,
and database connectivity.


