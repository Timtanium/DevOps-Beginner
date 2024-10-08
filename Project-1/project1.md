# Setup a Static Website Using Nginx

Welcome to your first project. Get ready to launch your very own static website. In this project, you'll be:

- **Building with Nginx:** We'll set up Nginx as your web server, the engine that delivers your website content to the world.
- **Connecting with Route53:** You'll learn how to configure Amazon Route53, the DNS service that directs visitors to your website's location.
- **Securing with Certbot:** We'll implement HTTPS encryption using Certbot, ensuring a safe and secure connection for your website.

By the end of this project, you'll have a fully deployed and secure static website ready to share with the world!

> [!NOTE]
An AWS account is required to carry out this project so ensure you get 1.

## Introduction

Before we begin the project, let's review some of the key concepts and tasks we'll be tackling.

### Nginx

Nginx (pronounced "engine-x") is a high-performance web server and reverse proxy server that is widely used for serving static content, load balancing, and handling reverse proxy tasks. It is known for its speed, stability, and low resource consumption, making it an excellent choice for high-traffic websites. Nginx can also be used as an HTTP cache, an email proxy server, and a load balancer for HTTP, TCP, and UDP traffic. Its flexibility and scalability make it a popular choice among developers and system administrators.

### Route53

Amazon Route 53 is a scalable and highly available Domain Name System (DNS) web service offered by Amazon Web Services (AWS). It is designed to route end-user requests to internet applications by translating human-readable domain names (like www.example.com) into IP addresses that computers use to connect to each other. Route 53 also allows you to:

- **Register Domain Names**: You can purchase and manage domain names directly through Route 53.
- **DNS Routing**: You can configure various routing policies, including simple, weighted, latency-based, failover, and geolocation routing to direct traffic based on different criteria.
- **Health Checks and Monitoring**: Route 53 can monitor the health of your web applications and automatically route traffic to healthy endpoints.
- **Integration with Other AWS Services**: It seamlessly integrates with other AWS services, making it easier to manage your infrastructure and improve application performance and reliability.

Route 53 is designed to provide developers and businesses with an extremely reliable and cost-effective way to route end users to internet applications.

### Certbot

Certbot is an open-source tool for automating the process of obtaining and renewing SSL/TLS certificates from the Let's Encrypt Certificate Authority (CA). These certificates are used to enable HTTPS on websites, which encrypts data transmitted between the server and the client's browser, enhancing security.

#### Key Features of Certbot:

- **Automated Certificate Management**: Certbot can automatically obtain and install certificates on your server.
- **Renewal Automation**: It can automatically renew certificates before they expire, ensuring continuous security.
- **Compatibility**: Certbot supports a wide range of web servers, including Apache and Nginx.
- **Ease of Use**: It simplifies the process of switching from HTTP to HTTPS by handling most of the complex details for you.

#### How Certbot Works:

1. **Install Certbot**: Install Certbot on your server.
2. **Obtain a Certificate**: Use Certbot to request a certificate from Let's Encrypt. Certbot will verify that you control the domain for which you are requesting the certificate.
3. **Install the Certificate**: Certbot can automatically configure your web server to use the new certificate.
4. **Renewal**: Certbot will periodically check for expiring certificates and renew them automatically.

Certbot is widely used because it provides a straightforward and automated way to improve web security with minimal effort.

---

## Project 1

|S/N | Project Tasks                                                                   |
|----|---------------------------------------------------------------------------------|
| 1  |Buy a domain name from a domain Registrar                                        |
| 2  |Spin up an Ubuntu server & assign an elastic IP to it                            |
| 3  |SSH into the server and install Nginx                                            |
| 4  |Download freely HTML website files(too plate) or use your personal code          |
| 5  |Copy the website files to the Nginx website directory                            |
| 6  |Validate the website using the server IP address                                 |
| 7  |In Route53, create an A record and add the Elastic IP                            |
| 8  |Using DNS verify the website setup                                               |
| 9  |Install certbot and Request For an SSL/TLS Certificate                           |
| 10 |Validate the website SSL using the OpenSSL utility                               |

## Key Concepts Covered

- AWS (EC2 and Route 53)
- EC2
- Linux(Ubuntu)
- Nginx
- DNS
- SSL (certbot)
- OpenSSL command

## Checklist

- [x] Task 1: Buy a domain name from a domain Registrar.
- [x] Task 2: Spin up a Ubuntu server & assign an elastic IP to it.
- [x] Task 3: SSH into the server and install Nginx.
- [x] Task 4: Find freely available HTML website files.
- [x] Task 5: Download and unzip the website files to the Nginx website directory.
- [x] Task 6: Validate the website using the server IP address.
- [x] Task 7: In Route53, create an A record and add the Elastic IP.
- [x] Task 8: Using DNS verify the website setup.
- [x] Task 9: Install certbot and Request For an SSL/TLS Certificate.
- [x] Task 10: Validate the website SSL using the OpenSSL utility.


## project 1 Documentation

### create an ubuntu server

- I located and clicked on **EC2** within the AWS management console.

- i then clicked on **Launch Instance**

- I **named** my instance and selected the **ubuntu** AMI.

![4](img/Screenshot%20(4).png)

- I clicked the **create new key pair** button and generated a key pair for secure connection to my isntance.

![6](img/Screenshot%20(6).png)

- I entered a **Key pair name** and clicked on **create key pair**.

- I enabled **SSH**, **HTTP**, **HTTPS** access, then prroceeded to click **Launch instance**.

> [!NOTE]
For security reasons, it's recommended to restrict SSH access to your IP address only. However, for the purpose of this documentation, access has been granted from anywhere.

- I clicked on **View all instances**.

- I clicked on the **created instance**.

- I clicked on the **connect** button.

![7](img/Screenshot%20(7).png)

- I copied the command provided under **SSH client**.

-I opened a terminal in the directory where my **.pem** file was ddownloaded, pasted the command and pressed enter.

---

> [!NOTE]
- link on **how to open terminal on your downloads folder on windows only:** (https://github.com/StrangeJay/devops-beginner-bootcamp/blob/6fe0c1a4fee9300bc92bba8325b06f7c91216c77/project1/project1.md?plain=1#L148).

### Create And Assign an Elastic IP

- I returned to my AWS console and clicked on the **menu icon** to open the dashboard menu.

- I selected **Elastic IPs** under **Network & Security**.
- I clicked on the **Allocate Elastic IP address** button, I left the settings unchanged and proceeded to click **Allocate**. I then associated the elastic IP address with my running instance.

> [!NOTE]
The IP address for my instance has been updated to the elastic IP associated with it. Therefore, I needed to SSH into my instance again. I returned to the connection page of my instance and copied the new command.

---

### Install Nginx and Setup Your Website

- I executed the following commands.

`sudo apt update`
`sudo apt upgrade`
`sudo apt install nginx`

- I started my Nginx server by running the **`sudo systemctl start nginx`**command, enabled it to start on boot by executing **`sudo systemctl enable nginx`**, and then confirmed if it's running with the **`sudo systemctl status nginx`** command.

- I went back to my EC2 dashboard and copied my **Public IPv4 address**.

![10](img/Screenshot%20(10).png)

- I visited my instances **Public IPv4 address** in a web browser to view the default Nginx startup page.

![11](img/Screenshot%20(11).png)

- I downloaded my website template from my preferred website by navigating to the website, locating the template I want, and obtaining the download URL for the website.

---

**How to obtain the website template URL from tooplate.com:**

- Visit [**Tooplate**](https://www.tooplate.com/) and select the website template you prefer.
- Scroll down to the download section, right-click to open the menu, and select **Inspect** from the options.
- Select the **Network** tab.
- Click the **Download** button.
- You’ll see the **website zip folder** appear. Hover your mouse or trackpad pointer over it and right-click again.

> [!NOTE]
I made sure I right-clicked on the zip folder, the one that says **.zip**. And when it didn't appear after clicking download, I tried clicking the download button again until it showed up.
- I hovered my mouse cursor over **Copy①** and then clicked on **Copy URL②** from the list that appeared on the right.
- I pasted the URL into a notebook to use alongside the **`curl`** command when downloading the website content to my machine.

- I ran this command **`sudo curl -o /var/www/html/2098_health.zip https://www.tooplate.com/zip-templates/2098_health.zip`** to download the websites file to my html directory.

- To install the unzip tool, I ran the following command: **`sudo apt install unzip`**.

- I navigated to the web server directory by running the following command: **`cd /var/www/html`**.

- I unzipped the contents of my website by running **`sudo unzip <website template name>`**.

> [!NOTE]
I replaced **`<website template name>`** with the actual name of my website zip file. For example, mine is **2098_health.zip** so i ran **`sudo unzip 2098_health.zip`**.
- I updated my nginx configuration by running the command **`sudo nano /etc/nginx/sites-available/default`**. Then, I edited the **`root`** directive within my server block to point to the directory where my downloaded website content is stored.

- I restarted Nginx to apply the changes by running: **`sudo systemctl restart nginx`**.

![22](img/Screenshot%20(22).png)
- I opened a web browser and went to my **Public IPv4 address/Elastic IP address** to confirm that my website is working as expected.

![13](img/Screenshot%20(13).png)

---

### Create An A Record

To make my website accessible via my domain name rather than the IP address, I needed to set up a DNS record. I did this by buying my domain from Qservers and then moving hosting to AWS Route 53, where I set up an A record.

![14](img/Screenshot%20(14).png)

- On the website I clicked on **Domain List**.
- I clicked on the **Manage** button.

- I went back to my AWS console, searched for **Route 53①**, and then chose **Route 53②** from the list of services shown.
- I clicked on **Get started**, selected **create hosted zones①** and I clicked on **Get started②**.

- I entered my **Domain name①**, chose **Public hosted zone②** and then I clicked on **Create hosted zone③**.
- I selected the **created hosted zone①** and copied the assigned **Values②**.
- I went back to my domain registrar and selected **Custom DNS** within the **NAMESERVERS** section.
- I pasted the values I copied from Route 53 into the appropriate fields, then clicked the **checkmark symbol** to save the changes.
- I went back to my AWS console and clicked on **Create record**, pasted my Elastic IP address and then clicked on **create records**.

- My A record has been created successfully.

![15](img/Screenshot%20(15)-1.png)
![16](img/Screenshot%20(16).png)
![17](img/Screenshot%20(17).png)
![18](img/Screenshot%20(18).png)

- I clicked on **create record** again, to create the record for my sub domain.
- I put the Record name(**www➀**), pasted my **IP address➁**, and then clicked on **Create records➂**.

> [!NOTE]
I made sure to create DNS records for both my root domain and subdomain. This involves setting up an A record for the root domain (e.g., **`example.com`**) and another A record for the subdomain (e.g., **`www.example.com`**). These records will direct traffic to my server's IP address, ensuring that both my main site and any subdomains are accessible.
- I opened my terminal and ran **`sudo nano /etc/nginx/sites-available/default`** to edit my settings. I entered my domain and subdomain names, then saved the changes.

- I restarted my nginx server by running the **`sudo systemctl restart nginx`** command.

- I went to my domain name in a web browser to verify that my website is accessible.

> [!NOTE]
I noticed the sign that says **Not secure**. Next, I'll use certbot to obtain the SSL certificate necessary to enable HTTPS on my site.

---

### Install certbot and Request For an SSL/TLS Certificate

- I Installed certbot by executing the following commands:
**`sudo apt update`**
**`sudo apt install certbot python3-certbot-nginx`**

- I executed the **`sudo certbot --nginx`** command to request my certificate. I followed the instructions provided by certbot and selected the domain name for which  like to activate HTTPS.
- I verified the website's SSL using the OpenSSL utility with the command: **`openssl s_client -connect timtanium.name.ng:443`**

![20](img/Screenshot%20(20).png)

- I visited **`https://timtanium.name.ng`** to view my website.

![21](project-1/img/)

---
---

#### The End!