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

---

# 1. Install and Configure NGINX

## Your NGINX learning environment

*   This chapter focuses on setting up a sandbox environment.
*   **Goals for this chapter:**
    1.  Install NGINX.
    2.  Become familiar with the NGINX command-line interface (CLI).
    3.  Deploy a professionally designed website.

## Install NGINX on Ubuntu

*   **Prerequisites:** A server running the **Ubuntu** operating system. The course uses a cloud-based server on AWS.
*   **Installation Steps:**
    1.  Connect to the server and gain administrative privileges. This command makes you the `root` user for the session.
        ```bash
        sudo su -
        ```
    2.  Update the package list to ensure you get the latest version information.
        ```bash
        apt update
        ```
    3.  Install the NGINX package.
        ```bash
        apt install nginx
        ```
        *   Press `Enter` to confirm the installation when prompted.

*   **Verifying the Installation:**
    1.  **Check the version:** The `-V` (uppercase) switch shows the version and configuration parameters it was compiled with.
        ```bash
        nginx -V
        ```
        *   Example output: `nginx version: nginx/1.18.0 (Ubuntu)`
    2.  **Check the service status:** Use `systemctl` to see if the NGINX service is running.
        ```bash
        systemctl status nginx
        ```
        *   Look for the status "active (running)".
        *   Press `q` to exit the status view.
    3.  **Use a web browser:** This is the most important confirmation.
        *   Open a browser and navigate to your server's public IP address or DNS name.
        *   You should see the **"Welcome to nginx!"** default page. This confirms the installation was successful and the server is running correctly.

## The NGINX command-line interface

*   **Permissions:** Managing the NGINX service requires elevated permissions, using `sudo` before each command or by becoming the `root` user with `sudo su`.
*   **Managing the Service with `systemctl`:**
    *   **Check Status:**
        ```bash
        systemctl status nginx
        ```
        *   To display the status without entering the pager interface, use the `--no-pager` flag:
          ```bash
          systemctl status nginx --no-pager
          ```
    *   **Stop/Start Service:**
        ```bash
        systemctl stop nginx
        systemctl start nginx
        ```
    *   **Reload Configuration:** This is the preferred way to apply configuration changes without causing downtime.
        ```bash
        systemctl reload nginx
        ```
        *   Reloading keeps the service running, whereas `stop` and `start` cause a brief service interruption.
        *   **Note:** The `start`, `stop`, and `reload` commands do not produce any output. Use `systemctl status nginx` to confirm the action was successful.

*   **Using the `nginx` Command Directly:**
    *   **Help:** To see a list of available command switches.
        ```bash
        nginx -h
        ```
    *   **Test Configuration (Syntax Check):** The lowercase `-t` switch checks configuration files for syntax errors before you try to reload them.
        ```bash
        nginx -t
        ```
        *   A successful check will return an "ok" message.
    *   **Test and Print Configuration:** The uppercase `-T` switch tests the configuration and then prints the entire, fully resolved configuration to the screen.
        ```bash
        nginx -T
        ```
        *   This is useful for seeing how all the included files are combined into a single configuration.
        *   You can pipe the output to a pager like `less` or `more` to view it one page at a time: `nginx -T | less`.

## NGINX files and directories

*   **Main Configuration Directory: `/etc/nginx/`**
    *   This directory holds all configuration for the NGINX installation.
    *   `nginx.conf`: The main, top-level configuration file.
    *   `conf.d/`, `sites-available/`, `sites-enabled/`: Directories that hold server configuration files (similar to Apache's virtual hosts).
    *   **Example Server Config:** `/etc/nginx/sites-available/default`
        *   This file contains the configuration for the "Welcome to nginx!" page.
        *   It includes helpful comments for getting started.

*   **Log Directory: `/var/log/nginx/`**
    *   This is the default location for NGINX log files.
    *   `access.log`: Records every request processed by the server, including the client's IP address, time, and the requested path.
    *   `error.log`: Records any errors encountered by the web server, as well as operational messages like restarts and configuration reloads.

*   **Web Content (Document Root) Directory: `/var/www/`**
    *   This location is used to store the actual files (HTML, images, etc.) served to clients.
    *   `/var/www/html/` is the default directory for the initial NGINX installation.
    *   As you define new server configurations, you will typically create new subdirectories within `/var/www/`.

## Inside nginx.conf

*   **Location:** `/etc/nginx/nginx.conf`
*   **Editing:** It is **rarely necessary** to edit this file directly. The instructor uses `view` (read-only vim) to inspect it safely.
*   **File Structure:**
    *   **Simple Directives:** A setting on a single line, ending with a semicolon (e.g., `user nginx;`).
    *   **Block Directives:** A directive that contains other directives within an opening and closing curly bracket `{ }` (e.g., the `http` block).

*   **Key Directives and Sections:**
    *   `user`: Defines the system user account that NGINX worker processes will run as (e.g., `user www-data;`).
    *   `http { ... }`: A block that contains global directives for how NGINX handles HTTP traffic.
    *   `include`: A directive used to load configuration from other files, making the setup modular.

*   **Configuration Loading Methods (`include` statements):**
    1.  `include /etc/nginx/conf.d/*.conf;`
    2.  `include /etc/nginx/sites-enabled/*;`
*   **Schools of Thought on Custom Configurations:**
    *   **`sites-enabled` Method (Ubuntu Default):**
        1.  Create configuration files in `/etc/nginx/sites-available/`.
        2.  Create a symbolic link (`ln -s`) to the file from within the `/etc/nginx/sites-enabled/` directory to activate it.
    *   **`conf.d` Method (Course Standard):**
        1.  Place configuration files directly into `/etc/nginx/conf.d/`, ensuring they end with a `.conf` extension.
        2.  The instructor finds this method **more straightforward and easier to maintain**. This course will use the `conf.d` method.

*   **Further Reading:** The official NGINX documentation at **nginx.org** contains a complete list of all available directives.

## Configure a virtual host, part 1

*   **Goal:** Create the first custom server configuration for a new website.

*   **Step 1: Disable the Default Site**
    *   The default NGINX configuration is enabled via a symbolic link in `/etc/nginx/sites-enabled/`.
    *   Remove the link to disable the default welcome page.
        ```bash
        # Navigate to the directory (optional, but good practice)
        cd /etc/nginx/sites-enabled/
        
        # Remove the symbolic link
        unlink default
        ```
    *   The original configuration file remains untouched in `/etc/nginx/sites-available/` if you need to restore it later.

*   **Step 2: Create a New Configuration File**
    *   **Location:** `/etc/nginx/conf.d/` (This is the method used in the course).
    *   **Naming Convention:**
        *   Name the file after the site for easier management (e.g., `binaryville.conf`).
        *   The file **must** end with the `.conf` extension to be loaded by NGINX.
    *   To disable a site using this method, you can either delete its `.conf` file or rename it to not end in `.conf`.
    *   Create the new file using an editor like `vim`:
        ```bash
        vim /etc/nginx/conf.d/binaryville.conf
        ```

*   **Step 3: Build the Basic Server Block**
    *   A `server` block defines the configuration for a single website (virtual host).
    *   **`listen` directive:** Tells NGINX which port to listen on for requests. Port `80` is the standard for HTTP.
    *   **`root` directive:** Specifies the document root—the directory on the server where the website's files are stored.
    *   Example initial configuration:
        ```nginx
        server {
            listen 80;
            root /var/www/binaryville;
        }
        ```

*   **Step 4: Test and Apply the New Configuration**
    1.  Save the configuration file.
    2.  **Test for syntax errors:**
        ```bash
        nginx -t
        ```
        *   You should see a message like: `nginx: the configuration file /etc/nginx/nginx.conf syntax is ok` and `nginx: configuration file /etc/nginx/nginx.conf test is successful`.
    3.  **Reload the NGINX service** to apply the changes without downtime:
        ```bash
        systemctl reload nginx
        ```
    4.  **Confirm the reload** was successful by checking the service status:
        ```bash
        systemctl status nginx
        ```
        *   Look for a line at the end confirming the service was reloaded.

*   **Step 5: Set Up the Website Directory and a Test File**
    1.  Create the document root directory specified in the config file. The `-p` flag creates parent directories if they don't exist.
        ```bash
        mkdir -p /var/www/binaryville
        ```
    2.  Place a simple `index.html` file in the new directory to confirm it's working.
        ```bash
        echo "site coming soon" > /var/www/binaryville/index.html
        ```
    3.  Refresh the browser page for your server's IP/DNS. You should now see the "site coming soon" message.

## Configure a virtual host, part 2

*   **Goal:** Add more essential directives to the `binaryville.conf` server block for a more robust configuration.

*   **New Directives to Add:**
    1.  **`default_server`:**
        *   Added to the `listen` directive: `listen 80 default_server;`
        *   **Purpose:** If a request comes in that does not match any other `server_name` configured on the server's IP, this server block will be used to handle it.
    2.  **`server_name`:**
        *   Example: `server_name binaryville.local www.binaryville.local;`
        *   **Purpose:** Defines which domain names this server block is responsible for. This is crucial for hosting multiple websites on a single IP address (named-based virtual hosts).
    3.  **`index`:**
        *   Example: `index index.html index.php;`
        *   **Purpose:** Tells NGINX which file to serve by default when a client requests a directory. NGINX will look for the files in the order they are listed. `index.html` is the default if the directive is not specified.

*   **Updated `binaryville.conf`:**
    ```nginx
    server {
        listen 80 default_server;
        root /var/www/binaryville;

        index index.html;

        server_name binaryville.local www.binaryville.local;
    }
    ```

*   **Testing the Updated Configuration:**
    1.  Save the file.
    2.  Test syntax: `nginx -t`
    3.  Reload NGINX: `systemctl reload nginx`
    4.  Test locally from the server using `curl`:
        ```bash
        curl localhost
        ```
        *   This should return the content of your `index.html` file ("site coming soon").

## Add files to the root directory

*   **Goal:** Deploy the actual website files to the document root directory.
*   **Steps:**
    1.  **Get the Exercise Files:** Clone the course's GitHub repository to your server.
        ```bash
        git clone <repository_url>
        ```
    2.  **Locate the Website Archive:** The repository contains the website files, often in an archive format like `.zip` or `.tar.gz`. The course uses the tar archive.
    3.  **Extract the Files:** Use the `tar` command to extract the archive's contents directly into the website's root directory (`/var/www/binaryville`). The course provides the exact command.
        *   Example command structure: `tar -xzvf [archive-name].tar.gz -C /var/www/binaryville --strip-components=1`
    4.  **Verify Files:** List the contents of the root directory to ensure the files were extracted correctly.
        ```bash
        ls /var/www/binaryville
        ```
    5.  **View the Live Site:** Refresh the page in your browser. The full, styled Binaryville website should now be displayed.

## Challenge: Set up an NGINX server on Ubuntu Linux

*   **The Task:** A developer needs a server for a new website. Your job is to set up a basic, working NGINX server on Ubuntu.
*   **Requirements:**
    1.  Start with a server running the latest version of Ubuntu.
    2.  Install NGINX.
    3.  Use CLI commands to verify the service is running and the configuration is valid.
    4.  Access the server's URL in a browser to confirm the default NGINX welcome page is being served.

## Solution: Set up an NGINX server on Ubuntu Linux

*   **Steps to Complete the Challenge:**
    1.  Connect to your Ubuntu server.
    2.  Gain root privileges:
        ```bash
        sudo su -
        ```
    3.  Update the system's package list:
        ```bash
        apt update
        ```
    4.  Install NGINX. The `-y` flag automatically answers "yes" to the confirmation prompt.
        ```bash
        apt install -y nginx
        ```
    5.  Check the status of the NGINX service:
        ```bash
        systemctl status nginx
        ```
        *   Look for "active (running)".
    6.  Test the default NGINX configuration for syntax errors:
        ```bash
        nginx -t
        ```
        *   Look for the "syntax is ok" and "test is successful" messages.
    7.  Open a web browser and navigate to the server's public IP address or DNS name. The "Welcome to nginx!" page should be displayed.

## Chapter Quiz

*   **Q1: What is the main configuration file for NGINX?**
    *   **A:** `/etc/nginx/nginx.conf`. It contains the main server configuration and can include other configuration files.
*   **Q2: Is opening a browser to the server's IP address a good way to confirm NGINX is installed correctly?**
    *   **A:** TRUE. If the installation is correct, the default welcome page will be served.
*   **Q3: What is the file extension for configuration files in `/etc/nginx/conf.d`?**
    *   **A:** `.conf`. This extension is required for the `include` directive in `nginx.conf` to process them.
*   **Q4: What is the best practice for naming configuration files for different websites?**
    *   **A:** Name each configuration file after the site it defines (e.g., `mytotallyamazingwebsite.com.conf`).
*   **Q5: Must all NGINX settings exist in the `/etc/nginx.conf` file?**
    *   **A:** FALSE. The `include` directive is used to load configurations from other files and directories.
*   **Q6: Are the settings used to configure NGINX called directives?**
    *   **A:** TRUE. Settings are listed as simple directives (one line) or block directives (multiple lines in `{}`).
*   **Q7: Which directory holds the configuration for an NGINX installation?**
    *   **A:** `/etc/nginx`.
*   **Q8: What might happen if a `server_name` directive is missing when serving multiple sites from one IP?**
    *   **A:** NGINX might serve the wrong content, as it won't know which site the request is for.
*   **Q9: What does the command `systemctl reload nginx` do?**
    *   **A:** FALSE. It tells NGINX to reload all **configuration files** from disk, not the website's content (HTML, CSS, etc.).
*   **Q10: How does NGINX use the `default_server` directive?**
    *   **A:** It tells NGINX to use that server configuration if no other configuration's `server_name` matches the requested site.
*   **Q11: What is the command `nginx -t` used for?**
    *   **A:** To check the NGINX configuration for syntax errors without stopping or reloading the service.
*   **Q12: What does the directive `listen 80;` do?**
    *   **A:** It configures the server to listen for incoming web requests on port 80.

---

# Additional NGINX Configurations

## Additional configurations and troubleshooting

- **Beyond Basic Setup**: After installing and running NGINX, additional configurations enhance functionality
- **Key Areas Covered**:
  - **Locations**: Powerful feature to define how NGINX responds to different requests
  - **Custom Logging**: Write logs to specific files for better debugging
  - **Troubleshooting**: Tools and techniques when things don't work as expected
- **Purpose**: Get a custom installation that meets specific requirements

## Configure locations, part 1

### What are Location Directives?
- **Definition**: Directives that extend configuration based on the URI of incoming requests
- **Structure**: Formatted as blocks, defined inside server blocks
- **Nesting**: Can be nested inside other location blocks
- **Flexibility**: Include most directives that work in server blocks

### Location Directive Format
```nginx
location [modifier] [location_definition] {
    # Configuration directives
}
```

### Components Breakdown
- **location keyword**: Required starter
- **modifier**: Optional (controls how definition is interpreted)
- **location definition**: Required (URI pattern to match)
- **directives**: Configuration inside the block

### Location Modifiers

| Modifier | Name | Description | Behavior |
|----------|------|-------------|----------|
| `=` | Exact match | Matches definition exactly | No other locations checked |
| `~` | Case-sensitive regex | Regular expression matching | Case matters |
| `~*` | Case-insensitive regex | Regular expression matching | Case ignored |
| `^~` | Prefix (no regex) | Stops regex processing | Optimization modifier |
| (none) | Prefix | Treats as URI prefix | Default behavior |

### Processing Rules
- **Prefix strings**: Checked first
- **Regular expressions**: Checked second, in order of appearance
- **Best match**: NGINX uses series of matches to determine best location

### Match Priority Order
1. **Exact matches** (`=`)
2. **Prefix matches** (longest wins)
3. **Regular expressions** (first match wins)

## Configure locations, part 2

### Practical Configuration Example

#### Root Location Setup
```nginx
location / {
    try_files $uri $uri/ =404;
}
```

#### try_files Directive Breakdown
- **Purpose**: Gives NGINX list of files/directories to look for
- **Processing**: First match gets processed
- **Fallback**: Last item used if no matches (URI or error code)
- **Variables**:
  - `$uri`: Look for file matching the URI
  - `$uri/`: Test for directories matching URI
  - `=404`: Serve 404 error if nothing matches

#### Images Location with Auto-indexing
```nginx
location /images/ {
    autoindex on;
}
```
- **autoindex**: Lets browsers list files in directory
- **Default**: Turned off by default
- **Use case**: File browsing functionality

### Custom Error Pages Configuration

#### Error Page Directives
```nginx
error_page 404 /404.html;
error_page 500 502 503 504 /50x.html;
```

#### Error Page Locations
```nginx
location = /404.html {
    internal;
}

location = /50x.html {
    internal;
}
```

#### Key Features
- **Exact match modifier** (`=`): Ensures precise matching
- **internal directive**: Processes redirects as internal redirects
- **Performance**: Helps NGINX serve error pages more quickly

### Testing Location (500 Error Demo)
```nginx
location = /500 {
    fastcgi_pass unix:/nonexistent/socket;
}
```
- **Purpose**: Demonstrates 500 error handling
- **Misconfiguration**: Uses undefined Unix socket
- **Result**: Triggers 500 error for testing

### Testing Process
1. **Configuration test**: `nginx -t`
2. **Reload service**: `systemctl reload nginx`
3. **Browser testing**:
   - `/images` - Shows auto-indexed directory
   - `/missing` - Shows custom 404 page
   - `/500` - Shows custom 500 page

## Configure logs

### NGINX Logging Types
- **Access Logs**: Record request details
  - Time of request
  - Success/failure status
  - Client IP address
  - Browser type
- **Error Logs**: Record operational issues
  - Configuration errors
  - Service start/stop events
  - Runtime errors

### Default Logging Configuration
- **Location**: `nginx.conf` inside HTTP block
- **Directives**: `access_log` and `error_log`
- **Limitation**: All sites write to same logs
- **Problem**: Difficult to isolate site-specific issues

### Custom Logging Levels

#### Server-Level Logging
```nginx
server {
    access_log /var/log/nginx/mysite.access.log;
    error_log /var/log/nginx/mysite.error.log;
}
```

#### Location-Level Logging
```nginx
location /images/ {
    access_log /var/log/nginx/images.access.log;
    error_log /var/log/nginx/images.error.log;
}
```

### Best Practices
- **Descriptive naming**: Match log files to configuration names
- **Separate logs**: One per site for easier debugging
- **Granular logging**: Location-specific logs for detailed tracking

### Testing Custom Logs

#### Generate Test Requests
```bash
# Send 10 requests to test logging
for i in {1..10}; do curl localhost > /dev/null; done

# Test specific location
for i in {1..10}; do curl localhost/images/ > /dev/null; done
```

#### View Log Contents
```bash
# Check access logs
cat /var/log/nginx/mysite.access.log

# Check images location logs
cat /var/log/nginx/images.access.log
```

## Troubleshoot NGINX

### Configuration Testing
- **Primary Tool**: `nginx -t`
- **Benefits**:
  - Catches syntax errors
  - Identifies problem files
  - Shows line numbers
  - Validates directive usage

### Common Configuration Errors

#### Syntax Errors
- **Typos**: `servr` instead of `server`
- **File paths**: `HTM1` instead of `HTML`
- **Domain names**: `www.examples.com` vs `www.example.com`
- **Missing semicolons**: Most directives require them

#### Error Example
```nginx
# Wrong
servr {
    listen 80;
}

# Correct
server {
    listen 80;
}
```

### Service Status Troubleshooting

#### Check Service Status
```bash
systemctl status nginx
```
- **Output**: Confirms if NGINX is running
- **Follow-up**: Reload configuration if needed
```bash
systemctl reload nginx
```

#### Port Verification
```bash
# Using lsof (List Open Files)
lsof -i | grep nginx

# Using netstat (requires net-tools package)
netstat -plan | grep nginx
```

#### Standard Web Ports
- **Port 80**: HTTP traffic
- **Port 443**: HTTPS traffic

### Log-Based Troubleshooting

#### Real-time Log Monitoring
```bash
# Follow logs as they're written
tail -f /var/log/nginx/access.log
tail -f /var/log/nginx/error.log
```

#### Error Analysis
- **4xx errors**: Client-side issues (files not found, permissions)
- **5xx errors**: Server-side issues (configuration problems)
- **Access patterns**: Check if requests are being recorded

### Systematic Troubleshooting Steps
1. **Test configuration**: `nginx -t`
2. **Check service**: `systemctl status nginx`
3. **Verify ports**: `lsof -i | grep nginx`
4. **Review logs**: `tail -f /var/log/nginx/error.log`
5. **Test endpoints**: Try accessing various parts of site
6. **Seek help**: Search engines, Stack Overflow for specific errors

## Challenge: Customize an NGINX Configuration

### Requirements
- **Remove**: Default NGINX configuration
- **Add**: New configuration for status page
- **Route**: `/complete` endpoint (exact match, lowercase only)
- **Response**: "your request is complete" message
- **Logging**: Separate log file for this route

### Steps Overview
1. Start with fresh NGINX installation
2. Remove default configuration file
3. Add provided configuration to correct location
4. Restart NGINX
5. Update configuration for exact match location
6. Add custom logging to `complete.access.log`

### Time Estimate
- **Duration**: 15-20 minutes
- **Difficulty**: Intermediate

## Solution: Customize an NGINX Configuration

### Step-by-Step Solution

#### 1. Remove Default Configuration
```bash
# Navigate to sites-enabled directory
cd /etc/nginx/sites-enabled

# Remove default configuration link
unlink default
```

#### 2. Create Custom Configuration
```bash
# Navigate to conf.d directory
cd /etc/nginx/conf.d

# Create new configuration file
vim complete.conf
```

#### 3. Configuration Content
```nginx
server {
    listen 80 default_server;
    listen [::]:80;

    location = /complete {
        # Custom access log for this location
        access_log /var/log/nginx/complete.access.log;
        
        # Return JSON response
        return 200 '{"Message": "Your request is complete."}\n';
        
        # Set content type
        default_type text/json;
    }
}
```

#### 4. Configuration Breakdown
- **`location = /complete`**: Exact match for the endpoint
- **`access_log`**: Custom log file path
- **`return 200`**: HTTP 200 response with message
- **`default_type text/json`**: Sets response content type

#### 5. Test and Deploy
```bash
# Test configuration
nginx -t

# Reload NGINX
systemctl reload nginx

# Verify service status
systemctl status nginx
```

#### 6. Validation Steps
```bash
# Test the endpoint
curl localhost/complete

# Check log file creation
ls /var/log/nginx/complete.access.log

# View log contents
cat /var/log/nginx/complete.access.log
```

### Key Directives Used

#### access_log Directive
- **Purpose**: Tells NGINX where to write logs for the context
- **Scope**: Can be used in http, server, or location contexts
- **Documentation**: `http://nginx.org/en/docs/http/ngx_http_log_module.html#access_log`

#### return Directive
- **Purpose**: Stops processing and returns specified code with optional response
- **Syntax**: `return code [text];`
- **Documentation**: `http://nginx.org/en/docs/http/ngx_http_rewrite_module.html#return`

#### default_type Directive
- **Purpose**: Helps NGINX tell clients how to interpret response content
- **Usage**: Sets MIME type for responses
- **Documentation**: `http://nginx.org/en/docs/http/ngx_http_core_module.html#default_type`

## Chapter Quiz

### Question 1 of 7
**Location directives can not be nested inside other location blocks.**

- TRUE
- **FALSE** ✓

**Explanation**: Location directives are formatted as blocks and are defined inside server blocks. They can also be nested inside other location blocks.

### Question 2 of 7
**Where can logs be configured in NGINX configurations?**

- in location contexts
- **in the http, server, and location contexts** ✓
- in server contexts
- in server and location contexts

**Explanation**: Default logging is configured in the http context and can be overridden in the server and location contexts.

### Question 3 of 7
**What is a log file?**

- **a record of various operational details** ✓
- a list of the sites being served
- a record of the directives used to configure the server

**Explanation**: NGINX uses log files to record various operational details like the date, time, and name of a file that was served. These logs are useful for monitoring normal operation and tracking down issues if a problem occurs.

### Question 4 of 7
**A developer on your team has given you a new website to serve using NGINX. After reviewing the code, you find that the main file for the site is named index.xml. What directive in the site's NGINX configuration needs to be updated to allow the site to be served?**

- **try_files** ✓
- try_xml
- site_root
- listen

**Explanation**: The try_files directive gives NGINX a list of files or directories to look for, relative to the location. The first file or directory that matches gets processed. If no items in the list match, then the last item in the list is used as a URI or an error code.

### Question 5 of 7
**What error is displayed when NGINX can't find any content to respond to a request?**

- 400 error
- 403 error
- **404 error** ✓
- 200 error

**Explanation**: When NGINX can't find any content to respond to a request, it displays a 404 error page.

### Question 6 of 7
**What are location directive useful for?**

- configuring NGINX to process different requests in ways similar to server blocks without having to create additional servers
- **all of these answers** ✓
- extending an NGINX configuration based on the URI being processed by the server

**Explanation**: Location directives serve multiple purposes including extending configuration based on URI and allowing different request processing without additional servers.

### Question 7 of 7
**What is a good first step in troubleshooting problems with an NGINX server?**

- **Use the 'systemctl status nginx' command to make sure NGINX is running.** ✓
- Use the 'systemctl portcheck nginx' command to make sure NGINX is listening on port 80.
- Use the 'systemctl stop nginx' command to make sure NGINX is not running.

**Explanation**: A good first step in troubleshooting is to make sure NGINX is running. The systemctl status NGINX command gives output that confirms whether NGINX is running or not.

---

