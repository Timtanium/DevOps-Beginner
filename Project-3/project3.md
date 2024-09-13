# Project 3: Setup Load Balancing for Static Website Using Nignx

This project aims to teach Layer 7 load balancing and load balancing algorithms using Nginx as a Load Balancer.

## Type of Load Balancer

This is a reverse proxy load balancer configuration in Nginx, where Nginx acts as an intermediary for requests from clients seeking resources from the servers behind it. It's capable of distributing traffic across the servers listed in the upstream block.

## Characteristics

- **HTTP Load Balancing:** This configuration balances HTTP requests.

- **Round Robin:** By default, Nginx uses a round-robin algorithm to distribute traffic evenly among the servers unless otherwise specified.

- **Scalability:** You can add more servers in the upstream block to scale out your application.

>[!Note]
If you need more advanced load balancing methods (e.g., least connections, IP hash), you can configure Nginx to use those as well.

|S/N | Project Tasks                                                                                              |
|----|------------------------------------------------------------------------------------------------------------|
| 1  |Deploy three servers                                                                                        |
| 2  |Set up static websites on two servers using Nginx.                                                          |
| 3  |Use two separate HTML files with distinct content. Deploy one file to each server's index.html location.    |
| 4  |Set up Nginx on the third server. It will act as a load balancer.                                           |
| 5  |Configure Nginx to load and balance traffic between two static websites.                                    |
| 6  |Add the Nginx Load balancer IP to the DNS A record.                                                         |
| 7  |Try accessing the website. Every time you reload the website you should see a different index.html.         |

## Key Concepts Covered

- AWS (EC2 and Route 53)
- EC2
- Linux(Ubuntu)
- Nginx
- DNS
- **Load balancing**
- SSL (certbot)
- OpenSSL command

## Checklist

- [x] Task 1: Deploy three servers.
- [x] Task 2: Set up static websites on two servers using Nginx.
- [x] Task 3: Make a small change in the index.html file of one of the websites to differentiate between two servers OR For a clearer distinction, use two separate HTML files with distinct content.
- [x] Task 4: Set up Nginx on the third server. It will act as a load balancer.
- [x] Task 5: Configure Nginx to load and balance traffic between two static websites.
- [x] Task 6: Add the Nginx Load balancer IP to the DNS A record.
- [x] Task 7: Try accessing the website. Every time you reload the website you should see a different index.html.

## Documentation

I referenced [**Project1**](https://github.com/StrangeJay/devops-beginner-bootcamp/blob/main/project1/project1.md) for guidance on spinning up an Ubuntu server. I was meant to create an elastic ip and associate it to the server, but I did not to avoid racking up cost when not released.

- I spinned up 3 ubuntu servers and I ensured I clearly name them so I don't make mistakes.

### Installing Nginx and Website Setup

- I downloaded website template from my preferred website by navigating to the website, locating the template I want.

- I right clicked and selected **Inspect** from the drop down menu.

- I clicked on the **Network** tab.

- I clicked the **Download** button and **right clicked on the website name**.

- I selected **Copy** and clicked on **Copy URL**.

- To install Nginx, I executed the following commands on my terminal.

`sudo apt update`

`sudo apt upgrade`

`sudo apt install nginx`

- I started my Nginx server by running the `sudo systemctl start nginx` command, enabled it to start on boot by executing `sudo systemctl enable nginx`, and then confirmed if it's running with the `sudo systemctl status nginx` command.

> [!NOTE]
I installed Nginx on both web server terminals. These are the terminals I'll use to manage the servers hosting the two distinct website contents that the load balancer will distribute traffic to.

- I visited my instances IP address in a web browser to view the default Nginx startup page.

- I executed `sudo apt install unzip` to install the unzip tool and run the following command to download and unzip my website files `sudo curl -o /var/www/html/2135_mini_finance.zip https://www.tooplate.com/zip-templates/2135_mini_finance.zip && sudo unzip -d /var/www/html/ /var/www/html/2135_mini_finance.zip && sudo rm -f /var/www/html/2135_mini_finance.zip`.

- To set up my website's configuration, I started by creating a new file in the Nginx sites-available directory. I used the following command to open a blank file in a text editor: `sudo nano /etc/nginx/sites-available/finance`.

- I copied and pasted the following code into the open text editor.

```
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/html/2135_mini_finance;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

- I edited the `root` directive within the server block to point to the directory where my downloaded website content is stored.


- I created a symbolic link for both websites by running the following command.
`sudo ln -s /etc/nginx/sites-available/finance /etc/nginx/sites-enabled/`

- I ran the `sudo nginx -t` command to check the syntax of the Nginx configuration file, and when it was successful I ran the `sudo systemctl restart nginx` command.

- I repeated the process for the second website.

> [!NOTE]
To better identify the impact of the changes, I connected to the second server in a new terminal window. There, I updated the website content with something different. When I reloaded my website, the load balancer will distribute traffic, potentially sending me to the updated version on the second server. This made the differences between the two versions clearer.

- I executed `sudo apt install unzip` to install the unzip tool and run the following command to download and unzip my website files `sudo curl -o /var/www/html/2133_moso_interior.zip https://www.tooplate.com/zip-templates/2133_moso_interior.zip && sudo unzip -d /var/www/html/ /var/www/html/2133_moso_interior.zip && sudo rm -f /var/www/html/2133_moso_interior.zip`.

- To set up my website's configuration, I started by creating a new file in the Nginx sites-available directory. I used the following command to open a blank file in a text editor: `sudo nano /etc/nginx/sites-available/interior`.

- I copied and pasted the following code into the open text editor.

```
server {
    listen 80;
    server_name placeholder.com www.placeholder.com;

    root /var/www/html/2133_moso_interior;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

- I edited the `root` directive within the server block to point to the directory where my downloaded website content is stored.

- I created a symbolic link for both websites by running the following command.
`sudo ln -s /etc/nginx/sites-available/interior /etc/nginx/sites-enabled/`

- I ran the `sudo nginx -t` command to check the syntax of the Nginx configuration file, and when successful I ran the `sudo systemctl restart nginx` command.

> [!NOTE]
On my first server, I ran `sudo rm /etc/nginx/sites-enabled/default`, and on my second server, I ran `sudo rm /etc/nginx/sites-enabled/default`. That deleted the default site-enabled folders and enable Nginx to serve content from my specified website directories. If one doesn't delete these default folders, one will continue to see the default Nginx page.


- I ran the `sudo systemctl restart nginx` command to restart my server.

- I checked both IP addresses to confirm my website is up and running.

![finance](img/Screenshot%20(49).png)

![interior](img/Screenshot%20(50).png)

---

### Configuration The Load balancer

- I installed Nginx on the server I want to use as a load balancer, and executed `sudo systemctl status nginx` to ensure it's running.

- I executed `sudo nano /etc/nginx/nginx.conf` to edit my Nginx configuration file.

![51](img/Screenshot%20(51).png)

- I added the following within the http block.

```

    upstream timtanium {
    server (server 1 private ip);
    server (server 2 private ip);
    # Add more servers as needed
}

server {
    listen 80;
    server_name timtanium.name.ng www.timtanium.name.ng;

    location / {
        proxy_pass http://timtanium;
    }
}

```

![53](img/Screenshot%20(53).png)

> [!NOTE]
I replaced the necessary placeholders as shown in the picture above. I substituted `<server 1>` and `<server 2>` with the actual private IP addresses of my servers. Also, I replaced `<your domain> www.<your domain>` with my root domain and subdomain name, and update proxy_pass and the other relevant fields accordingly.

- I ran `sudo nginx -t` to check for syntax error.

![52](img/Screenshot%20(52).png)

- I applied the changes by restarting Nginx:
`sudo systemctl restart nginx`

---

### Creating An A Record

To make my website accessible via my domain name rather than the IP address, I'll need to set up a DNS record. I did this by buying my domain from qservers and then moving hosting to AWS Route 53, where I set up an A record.

> [!NOTE]
I visited [Project1](https://github.com/StrangeJay/devops-beginner-bootcamp/blob/main/project1/project1.md) for instructions on how to create a hosted zone.

- I pointed my domain's A records to the IP address of my Nginx load balancer server.

- In route 53, I selected the domain name and click on **Create record**.

- I pasted my **IP address➀** and then clicked on **Create records➁** to create the root domain.

- I clicked on **create record** again, to create the record for my sub domain.

- I pasted my IP address➀, input the Record name(**www➁**) and then clicked on **Create records**➂.

- I went to the terminal I used in setting my first website and ran `sudo nano /etc/nginx/sites-available/finance` to edit my settings. I entered the name of my domain and then saved my settings.

- I restarted my nginx server by running the `sudo systemctl restart nginx` command.

- I went to the terminal I used in setting my second website and ran `sudo nano /etc/nginx/sites-available/interior` to edit my settings. Entered the name of my domain and then saved my settings.

- I restarted my nginx server by running the `sudo systemctl restart nginx` command.

- I went to my domain name in a web browser to verify that my website is accessible.

- I reloaded the webpage to ensure the load balancer distributes traffic evenly between my servers.

![54](img/Screenshot%20(54).png)

![55](img/Screenshot%20(55).png)

---

### Install certbot and Request For an SSL/TLS Certificate

- I installed certbot by executing the following commands:
`sudo apt update`
`sudo apt install python3-certbot-nginx`

- I executed the `sudo certbot --nginx` command to request my certificate. I followed the instructions provided by certbot and selected the domain name for which I would like to activate HTTPS.

- I got a congratulatory message that says https has been successfully enabled.

![58](img/Screenshot%20(58).png)

- I accessed my website to verify that Certbot has successfully enabled HTTPS.

![https enabled](img/Screenshot%20(56).png) 

![https enabled](img/Screenshot%20(57).png)

- It is recommended to renew your LetsEncrypt certificate at least every 60 days or more frequently. You can test renewal command in dry-run mode:
`sudo certbot renew --dry-run`

---
---

#### The End Of Project 3