# Introduction

## What is NGINX?

*   **Definition:** NGINX is an open-source, high-performance web server that is used to power millions of websites and applications.
*   **Core Functions:**
    *   Excels at serving **static content**.
    *   Integrates with other applications to deliver **dynamic sites**.
    *   Can function as a **load balancer**.
*   **Course Instructor:** Michael Jenkins.
*   **Course Outline:**
    *   Install and configure NGINX.
    *   Deploy an e-commerce website to demonstrate NGINX features.
    *   Set up NGINX as a load balancer.
    *   Discuss NGINX security features for content protection.
    *   Use NGINX with **Docker** to create self-contained web applications.

## NGINX, Linux, and web technologies

*   **Prerequisites:**
    *   **Linux Experience:** It is recommended to have experience with the Linux operating system.
        *   **Required Skills:**
            *   Connecting to a Linux server.
            *   Using the command line interface (CLI).
            *   Editing files with an editor like `vim` or `nano`.
        *   **Recommended Refresher Course:** "Learning Linux Command Line" on LinkedIn Learning.
    *   **Web Technology Experience (Helpful):**
        *   Familiarity with general web server concepts and terms is beneficial.
        *   Experience with the **Apache HTTP web server** is particularly useful.
        *   **Recommended Overview Course:** "Linux Web Services" in the course library.

*   **Other Technologies Covered:**
    *   The course will utilize **HTML**, **Python**, and **Docker**.
    *   **Note:** Prior knowledge of these technologies is **not a requirement**. All necessary code will be provided.

*   **Course Resources:**
    *   **Exercise Files:**
        *   A **GitHub repository** is available with all necessary files, scripts, and references.
        *   You can download the files or clone the repository.
        *   The repository will also contain **troubleshooting tips** for potential issues in lessons.
    *   **Q&A Section:**
        *   An available forum to post questions and comments.
        *   Allows for interaction with other students and the instructor.
        *   You can ask for help or provide answers to others' questions.

## Comparing NGINX to Apache

### Introduction to Apache
*   The **Apache HTTP Server** (commonly known as Apache) is an open-source web server.
*   It has been a market leader for over 20 years.

### Similarities between NGINX and Apache

*   **Free and Open-Source:**
    *   Both are free to use without licensing fees.
    *   The source code is publicly available for review and modification.
    *   Strong community support for finding bugs, fixing vulnerabilities, and suggesting new features.
*   **Extensibility:**
    *   Both are compiled binaries but can be extended by dynamically loading modules at runtime to add functionality.
*   **Proxy Server Capabilities:**
    *   Both can be configured as proxy servers to pass requests to other applications and return the content to the client.
*   **Processing Model:**
    *   Both NGINX and **newer versions of Apache** use **event-based processing**.
    *   This model allows them to efficiently handle a large number of simultaneous connections.
    *   **Note:** Older versions of Apache used a resource-intensive model where each connection required a separate worker process, which could exhaust server resources under heavy load.

### Differences between NGINX and Apache

| Feature | NGINX | Apache |
| :--- | :--- | :--- |
| **Configuration Format** | **C-like syntax** with directives and blocks defined by curly brackets `{}`. Feels more like programming. | **XML-like format** with open and closing tags. Can be verbose. Feels more like writing a document. |
| **Configuration Structure** | **Centralized configuration.** All config files are loaded at startup. Uses `location` blocks to map requests to filesystems or other applications. | **Distributed configuration** via `.htaccess` files. These files can exist in any directory to override parent configurations. Can slow down performance as the file must be processed on each request. |
| **Dynamic Content** | **Relies on external applications** for dynamic processing (e.g., PHP-FPM). | **Can process dynamic content internally** by embedding interpreters for languages like PHP, Python, and Perl as modules. |
| **Static Content** | **Takes the lead in performance.** Benchmarks show NGINX is **more than twice as fast** as Apache at serving static files. | Slower than NGINX for static content. |
| **Caching** | **Has built-in caching capabilities** that are efficient and easy to configure. | Caching is possible via a module (`mod_cache`), but the configuration can be cumbersome. |

### Why Choose NGINX?

*   **Market Dominance:** NGINX has surpassed Apache to become the most widely used web server in the world.
*   **High Performance & Efficiency:**
    *   Operates efficiently and consistently, especially under heavy loads.
    *   Excellent for environments with limited resources (small CPUs, low RAM) due to its predictable performance.
*   **Ease of Configuration:**
    *   Many users find NGINX's configuration syntax easier to learn and manage compared to Apache's.
    *   This makes it a good choice for those new to application development and system administration.

## Set up a lab server

### Core Recommendations

*   **Dedicated Server Required:** You need access to a server to experiment with NGINX for this course.
*   **⚠️ CRITICAL WARNING:** **Do NOT use a production or live server.**
    *   The course activities will cause service interruptions.
    *   We may intentionally break things to learn how to fix them.
*   **Preferred Method: Public Cloud Server**
    *   Use a cloud service provider like **AWS, Azure, or Google Cloud**.
    *   Many providers offer free tiers, which are perfect for learning and experimentation.
    *   **Benefit:** This provides a realistic environment, similar to production, which is very different from running NGINX on a local machine.
*   **Operating System:** The course will use an **Ubuntu Linux** server.

### AWS Lab Server Setup Walkthrough

This section details the steps to set up a lab server using AWS EC2, as demonstrated in the course.

#### 1. Launch EC2 Instance

1.  Log into your AWS account and navigate to the **EC2 service** homepage.
2.  Click **Launch instance**.
3.  **Name:** Give the server a name (e.g., `nginx-sandbox`).
4.  **Application and OS Images (AMI):** Select **Ubuntu**.
    *   The course uses the latest LTS version at the time of recording: **Ubuntu 22.04 LTS**.
5.  **Instance Type:** Select **t2.micro**.
    *   This provides sufficient CPU and RAM for the course.
    *   It is eligible for the AWS Free Tier (750 hours/month).
6.  **Key Pair (for SSH access):**
    *   A key pair is required to connect to the server securely.
    *   Click **Create a new key pair**.
    *   **Key pair name:** `nginx-sandbox`
    *   **Private key file format:**
        *   `.pem` for macOS or Linux.
        *   `.ppk` for Windows users connecting with PuTTY.
    *   Click "Create key pair" and the file will be downloaded to your computer.
7.  **Network Settings (Security Group):**
    *   The security group acts as a virtual firewall for your instance.
    *   Give the security group a name (e.g., `nginx-sandbox`).
    *   Configure inbound rules to allow traffic on specific ports:
        *   ✅ **Allow SSH traffic** from anywhere (Port 22)
        *   ✅ **Allow HTTPS traffic** from anywhere (Port 443)
        *   ✅ **Allow HTTP traffic** from anywhere (Port 80)
8.  **Configure Storage:**
    *   The instructor recommends increasing the default storage to **20 GB** to provide more space.
9.  **Launch:**
    *   Review the summary of your settings.
    *   Click **Launch instance**.

#### 2. Allocate an Elastic IP (Static IP Address)

An Elastic IP ensures your server has a fixed public IP address that doesn't change if you stop and restart the instance.

1.  In the EC2 sidebar, go to **Network & Security** -> **Elastic IPs**.
2.  Click **Allocate IP address**.
3.  Add a **Tag** to easily identify it (e.g., Name: `nginx-sandbox`).
4.  Click **Allocate**.
5.  Select the newly created IP address and click **Associate this elastic IP address**.
6.  In the "Instance" dropdown, select the `nginx-sandbox` instance you just created.
7.  Click **Associate**.

#### 3. Connect to the Server via SSH

1.  **Get the Server Address:**
    *   Navigate back to your EC2 **Instances**.
    *   Select the `nginx-sandbox` instance.
    *   Copy the **Public IPv4 DNS** address.
2.  **Use a Terminal on Your Local Machine:**
    *   Open a terminal and navigate to the directory where your `.pem` key file was downloaded.
    *   **IMPORTANT:** Change the permissions of the key file. SSH will not work if the key is publicly viewable.
        ```bash
        # Makes the key file readable only by you
        chmod 600 nginx-sandbox.pem
        ```
    *   Connect to the server using the following SSH command format:
        ```bash
        ssh -i [your-key-file.pem] ubuntu@[your-server-dns-address]
        ```
    *   **Example:**
        ```bash
        ssh -i nginx-sandbox.pem ubuntu@ec2-xx-xx-xx-xx.compute-1.amazonaws.com
        ```
        *   `-i nginx-sandbox.pem`: Specifies the identity (key) file to use.
        *   `ubuntu`: The default username for Ubuntu instances on AWS.
        *   `@...`: The public DNS address of your server.

You are now connected to your lab server and ready to begin learning NGINX.