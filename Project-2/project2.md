# Setup Multiple Static Websites on a Single Server Using Nginx Virtual Hosts

|S/N | Project Tasks                                                                            |
|----|------------------------------------------------------------------------------------------|
| 1  |Install and configure Nignx on a server                                                   |
| 2  |Create two website directories with two different website templates.                      |
| 3  |Create two subdomains                                                                     |
| 4  |Add the IP of the server as A record to the two subdomains.                               |
| 5  |Configure the Virtual host to point two subdomains to two different website directories.  |
| 6  |Validate the setup by accessing the subdomains.                                              |
| 7  |Create a certbot SSL certificate for the root Domain.                        |
| 8  |Configure certbot on Nginx for two websites.                                         |
| 9  |Validate the subdomain websites’ SSL using OpenSSL utility.                               |

## Key Concepts Covered

- AWS (EC2 and Route 53)
- EC2
- Linux(Ubuntu)
- Nginx
- DNS
- **Subdomains**
- SSL (certbot)
- OpenSSL command

## Documentation

Please reference [**Project1**](https://github.com/StrangeJay/devops-beginner-bootcamp/blob/main/project1/project1.md) for guidance on spinning up an Ubuntu server, as well as creating and associating an elastic IP address with your server, among other tasks.

- Spin up your ubuntu server, create an elastic IP and associate it to your instance.
![23](img2/Screenshot%20(23).png)
![24](img2/Screenshot%20(24).png)

### Install Nginx and Setup Your Website

- Execute the following commands.

`sudo apt update`

`sudo apt upgrade`

`sudo apt install nginx`

- Start your Nginx server by running the `sudo systemctl start nginx` command, enable it to start on boot by executing `sudo systemctl enable nginx`, and then confirm if it's running with the `sudo systemctl status nginx` command.

- Visit your instances IP address in a web browser to view the default Nginx startup page.

![25](img2/Screenshot%20(25).png)
- Download your website template from your preferred website by navigating to the website, locating the template you want.

- Right click and select **Inspect** from the drop down menu.

- Click on the **Network** tab and then click **Download** button.

- Right click on the website name, select **Copy** and click on **Copy link address**.

- To install the unzip tool, run the following command: **`sudo apt install unzip`**.

- Execute the command to download and unzip your website files `sudo curl -o /var/www/html/2098_health.zip https://www.tooplate.com/zip-templates/2098_health.zip && sudo unzip -d /var/www/html/ /var/www/html/2098_health.zip && sudo rm -f /var/www/html/2098_health.zip`.

---

In summary, this command downloads the website template file, extracts its contents to the web server directory, and then deletes the downloaded zip file to clean up the directory.

---

- Download the 2nd website template by running the following command:

```
sudo curl -o /var/www/html/2132_clean_work.zip https://www.tooplate.com/zip-templates/2132_clean_work.zip && sudo unzip -d /var/www/html/ /var/www/html/2132_clean_work.zip && sudo rm -f /var/www/html/2132_clean_work.zip
```

> [!NOTE]
Replace the placeholders in the code with your own website URL. For example, substitute **`https://www.tooplate.com/zip-templates/2132_clean_work.zip`** with your chosen URL, and change **`2132_clean_work.zip`** accordingly.

- To set up your website's configuration, start by creating a new file in the Nginx sites-available directory. Use the following command to open a blank file in a text editor: **`sudo nano /etc/nginx/sites-available/cleaning`**


- Copy and paste the following code into the open text editor.

```
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/html/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

- Edit the `root` directive within your server block to point to the directory where your downloaded website content is stored.

![26](img2/Screenshot%20(26).png)

- Configure your second website by creating a new file in the Nginx sites-available directory with the following command: `sudo nano /etc/nginx/sites-available/health`.

- Copy and paste the following code into the open text editor.

```
server {
    listen 80;
    server_name placeholder.com www.placeholder.com;

    root /var/www/html/placeholder.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

- Edit the **`root`** directive within your server block to point to the directory where your downloaded website content is stored.

- Create a symbolic link for both websites by running the following command.
`sudo ln -s /etc/nginx/sites-available/cleaning /etc/nginx/sites-enabled/`
`sudo ln -s /etc/nginx/sites-available/health /etc/nginx/sites-enabled/`

- Run the **`sudo nginx -t`** command to check the syntax of the Nginx configuration file.

- Delete the default files in the sites-available and sites-enabled directories by executing the following commands:

```
sudo rm /etc/nginx/sites-available/default
sudo rm /etc/nginx/sites-enabled/default
```

- Restart the Nginx server by executing the following command: **`sudo systemctl restart nginx`**.

![27](img2/Screenshot%20(27).png)
---

### Create An A Record

To make your website accessible via your domain name rather than the IP address, you'll need to set up a DNS record. I did this by buying my domain from Namecheap and then moving hosting to AWS Route 53, where I set up an A record.

> [!NOTE]
Visit [**Project1**](https://github.com/StrangeJay/devops-beginner-bootcamp/blob/main/project1/project1.md) for instructions on how to create a hosted zone.

- In route 53, select the domain name and click on **Create record**.

- Paste your **IP address①** and then click on **Create records②**.

- Click on **Create record** again, to create the record for your sub domain.

- Input the **Record name①**, paste your **IP address②** and then click on **Create records③**.

- Repeat the same process while creating your second subdomain record, and confirm that they both exist in the records list.

![35](img2/Screenshot%20(35).png)

- Open your terminal and run **`sudo nano /etc/nginx/sites-available/cleaning`** to edit your settings. Enter the name of your domain and then save your settings.

- Run **`sudo nano /etc/nginx/sites-available/health`** to edit your settings. Enter the name of your domain and then save your settings.

- Restart your nginx server by running the **`sudo systemctl restart nginx`** command.

- Go to your domain name in a web browser to verify that your website is accessible.

![36](img2/Screenshot%20(36).png)

![37](img2/Screenshot%20(37).png)

> [!NOTE]
You may notice the sign that says **Not secure**. Next, you'll use certbot to obtain the SSL certificate necessary to enable HTTPS on your site.

---

### Install certbot and Request For an SSL/TLS Certificate

- Install certbot by executing the following commands:
`sudo apt update`
`sudo apt install python3-certbot-nginx`
`sudo certbot --nginx`


- Execute the **`sudo certbot --nginx`** command to request your certificate. Follow the instructions provided by certbot and select the domain name for which you would like to activate HTTPS.

> [NOTE]
In this case, SSL certificates were only created for **`cleaning.cloudghoul.online`** and **`health.cloudghoul.online`**. So, when prompted, I entered the numbers 1 and 3 (corresponding to those two domains) to select them for certificate generation. If we had also created records for **`www.cleaning.cloudghoul.online`** and **`www.health.cloudghoul.online`**, I could have simply pressed Enter to accept the default selection (**all available records**). If you try to create a certificate without having created an A record first, you will receive an error message.

- Verify the website's SSL using the OpenSSL utility with the command: **`openssl s_client -connect cleaning.cloudghoul.online:443`**


- Visit **`https://cleaning.timtanium.name.ng`**, **`https://health.timtanium.name.ng`** to view my websites.

![40](img2/Screenshot%20(40).png)

![41](img2/Screenshot%20(41).png)

---
---

#### The End Of Project 2