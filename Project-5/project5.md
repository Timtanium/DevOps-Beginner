# Setup Service Discovery Using Nginx & Consul

In DevOps, service discovery is all about automating how services find each other on a network. It's especially important in modern architectures with microservices, where applications are built from many small, independent services.

Traditionally, services were hardcoded with the locations of other services (like IP addresses). This becomes a nightmare to manage as things change and services are dynamically scaled up or down.

## Introduction

Before we start, let's go over the basics of service discovery and introduce the service discovery tool we'll be using for this project.

### Key Concepts of Service Discovery

1. **Service Registration:** This involves adding a new service instance to the service registry so that it can be discovered by other services. Each service instance must provide its network location (e.g., IP address and port) and other metadata.

2. **Service Registry:** This is a database of available service instances. It acts as a central repository where all the service instances are registered and their statuses are monitored. Examples include Consul, Eureka, and etcd.

3. **Service Lookup/Discovery:** This is the process by which a service finds the network locations of other services it needs to communicate with. Discovery can be done in several ways:

   - **Client-Side Discovery:** The client is responsible for querying the service registry to find an available service instance and then makes a request directly to that instance.

   - **Server-Side Discovery:** The client makes a request to a load balancer or API gateway, which queries the service registry and forwards the request to an available service instance.

4. **Health Checks:** These are used to monitor the health and availability of service instances. Services that fail health checks can be removed from the service registry to ensure that clients do not attempt to communicate with unavailable services.

### Benefits of Service Discovery

- **Dynamic Scalability:** As services scale up and down, service discovery ensures that the correct instances are always available.

- **Fault Tolerance:** By continually monitoring service health and availability, service discovery helps maintain robust communication between services.

- **Simplified Configuration:** Instead of hardcoding service locations, developers can use the service registry, making configuration simpler and more maintainable.

### Implementations

- **Consul:** A service mesh solution that provides service discovery, health checking, and a distributed key-value store.

- **Eureka:** A REST-based service used for locating services for the purpose of load balancing and failover in the cloud, often used with Spring Cloud.

- **etcd:** A distributed key-value store that can be used for service discovery, among other things.

- **ZooKeeper:** a powerful tool used to implement service discovery in distributed systems, providing essential coordination features such as centralized configuration management, naming services, and distributed synchronization.

### Example Workflow

- **Service Registration:** When a new service instance starts, it registers itself with the service registry, providing its address and metadata.

- **Health Checks:** The service registry periodically checks the health of registered services.

- **Service Discovery:** When a service needs to communicate with another service, it queries the service registry to find available instances.

- **Load Balancing:** If multiple instances of a service are available, the client or a load balancer can distribute requests among them.

### Use Cases

- **Microservices:** Ensuring that microservices can find each other dynamically as they scale and move across different nodes in a cluster.

- **Dynamic Environments:** In environments where services are frequently added, removed, or updated, service discovery helps maintain seamless communication.

Service discovery is an essential part of modern DevOps practices, enabling the efficient and reliable communication of services in dynamic and scalable environments.

### Consul

Consul is an open-source tool by HashiCorp for service discovery, configuration management, and network automation in distributed systems. It allows services to register and discover each other, performs health checks, and provides a distributed key/value store for configuration data. Consul supports secure service-to-service communication, multi-datacenter setups, and advanced service mesh capabilities, making it ideal for managing microservices and dynamic cloud environments.

---

## Project 5

|S/N | Project Tasks                                      |
|----|----------------------------------------------------|
| 1  |Deploy 4 Ubuntu Server                              |
| 2  |Allow required ports in the security group          |
| 3  |Set up architecture                                 |
| 4  |Setup Consul Server                                 |
| 5  |Setup Backend Servers                               |
| 6  |Setup Load-Balancer                                 |
| 7  |Validate Service Discovery Setup                    |

## Key Concepts Covered

- AWS (EC2 and Route 53)
- Linux(Ubuntu)
- Nginx
- Consul
- Environment Setup
- Service Registration with Consul
- Health Checks and Failover
- Load Balancing
- Monitoring and Logging
- Testing and Validation

## Checklist

- [x] Task 1: Deploy 4 Ubuntu Server
- [x] Task 2: Allow required ports in the security group
- [x] Task 3: Set up architecture
- [x] Task 4: Setup Consul Server
- [x] Task 5: Setup Backend Servers
- [x] Task 6: Setup Load-Balancer
- [x] Task 7: Validate Service Discovery Setup

## Documentation

I referenced [**Project1**](https://github.com/StrangeJay/DevOpsMastery/blob/main/project1/project1.md) for guidance on spinning up an Ubuntu server.

- I renamed my EC2 instances to prevent any confusion during my project.

![107](img/Screenshot%20(107).png)

- I clicked on the **edit icon**.

![108](img/Screenshot%20(108).png)
 
- I named my server and clicked the **checkmark icon**.

- I named my Consul server, LoadBalancer server, and the two backend servers for easy identification.

![107](img/Screenshot%20(107).png)

### Allow Required Ports In The Security Group

The Consul service requires specific ports to function correctly. I opened the following ports in my security group.

### Consul Servers

|S/N |Port Name  |Protocol      |Default Port   |
|----|-----------|--------------|---------------|
| 1  |DNS        |TCP and UDP   |8600           |
| 2  |HTTP API   |TCP           |8500           |
| 3  |HTTPS API  |TCP           |8501           |
| 4  |gRPC       |TCP           |8502           |
| 5  |gRPC TLS   |TCP           |8503           |
| 6  |Server RPC |TCP           |8300           |
| 7  |LAN Serf   |TCP and UDP   |8301           |
| 8  |WAN Serf   |TCP and UDP   |8302           |

- I selected the **checkbox①** next to my instance, clicked on **Security②**, and then clicked on the **security group ID③**.

- I clicked on **Edit inbound rules**.

- I clicked on **Add rule**.

- I entered the **Port range**.

![110](img/Screenshot%20(110).png)

> [!NOTE]
This port supports both TCP and UDP protocols, so I needed to configure both.

- I chose the appropriate CIDR block.

![111](img/Screenshot%20(111).png)

> [!NOTE]
For the purpose of this project, security measures have been intentionally relaxed by opening SSH, HTTP, HTTPS, and Consul ports to all traffic (0.0.0.0) for rapid development and testing. This configuration is highly insecure and should never be used in production environments.
In a production setting, it is crucial to:
Create separate security groups for each type of instance (consul server, backend server, load balancer), strictly limit inbound and outbound traffic to necessary ports and IP addresses, and implement additional security measures like network ACLs, IAM roles, and encryption.
By following these guidelines, you can significantly enhance the security of your infrastructure and protect your systems from unauthorized access.

- I clicked on **Add Rule** to specify the port range for the UDP protocol.

- I clicked on the **Type field①** and chose **Custom UDP**② from the dropdown menu.

- I entered the **Port range** and chose the **CIDR blocks**.

> [!NOTE]
I repeated this process until I've opened up all necessary ports.

- I verified that all the necessary ports are open.

![112](img/Screenshot%20(112).png)

- I clicked on **Save rules** to apply the updated security group settings.

![113](img/Screenshot%20(113).png)
> [!NOTE]
Currently, the ports are being opened manually one at a time. However, in future projects, you'll learn how to automate these tasks, such as creating multiple instances and configuring all the security group rules at once using code. You'll explore this further when you work with Terraform.

---

### Setup Consul Server

- I SSH into the consul server and ran **`sudo apt update`** to refresh the package cache.

- I visited the consul [**downloads**](https://developer.hashicorp.com/consul/install) page to **copy** the installation command.

Or execute the following commands to install Consul.

```
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install consul
```

- I confirmed Consul installation by checking its version with the **`consul --version`** command.

![116](img/Screenshot%20(116).png)

- All the Consul server configurations are located in the **`/etc/consul.d`** folder. To configure the Consul server, I start by backing up the default configuration file **`consul.hcl`** by renaming it to **`consul.hcl.back`**, using the following command: **`sudo mv /etc/consul.d/consul.hcl /etc/consul.d/consul.hcl.back`**

![117](img/Screenshot%20(117).png)

- I generated an **encrypted key** using the **`consul keygen`** command.

![118](img/Screenshot%20(118).png)

- I created a new file named **`consul.hcl`** in the **`/etc/consul.d`** directory, using the following command: **`sudo vi /etc/consul.d/consul.hcl`**

- I added the following content to the **`consul.hcl`** file, replacing **<YOUR_ENCRYPTED_KEY>** with the encrypted key I generated:

```
"bind_addr" = "0.0.0.0"
"client_addr" = "0.0.0.0"
"data_dir" = "/var/consul"
"encrypt" = "<YOUR_ENCRYPTED_KEY>"
"datacenter" = "dc1"
"ui" = true
"server" = true
"log_level" = "INFO"
```

>  [!NOTE]
- To write in this type of file, press letter 'i', when done editing press the 'esc' button, then press ':wq' and then enter to save.

Save this file after adding the content.

---

**Here's an explanation of each configuration setting in the Consul configuration file:**

1. **`bind_addr = "0.0.0.0"`**: Specifies the IP address on which Consul will bind to listen for incoming connections. `0.0.0.0` means Consul will listen on all available network interfaces.

2. **`client_addr = "0.0.0.0"`**: Determines the IP address on which the Consul client API will be available. Setting it to `0.0.0.0` allows connections from any IP address.

3. **`data_dir = "/var/consul"`**: Specifies the directory where Consul will store its data, such as the state and logs.

4. **`encrypt = "<YOUR_ENCRYPTED_KEY>"`**: Sets the encryption key for securing communication between Consul servers and clients. Replace this placeholder with your actual generated encryption key.

5. **`datacenter = "dc1"`**: Defines the datacenter name that this Consul server will use. Consul uses datacenters to organize services and nodes.

6. **`ui = true`**: Enables the Consul Web UI. This provides a graphical interface for interacting with Consul's data.

7. **`server = true`**: Indicates that this instance is a Consul server. Server nodes participate in the consensus protocol and store the state of the system.

8. **`log_level = "INFO"`**: Sets the verbosity of the logs. `INFO` level provides a balance of details, logging general information, warnings, and errors.

---

- I ran the following command to start the Consul server in the background: **`sudo nohup consul agent -dev -config-dir /etc/consul.d/ &`**.

> [!NOTE]
We use the **`-dev`** flag to indicate that we are running a single Consul server in development mode.

- I checked the status of the Consul server with the following command: **`consul members`**.

![120](img/Screenshot%20(120).png)

- I visited my **`<EC2 Consul Server IP>:8500`**, and I'm able to access the Consul dashboard.

> [!NOTE]
I ensured I replaced **`<EC2 Consul Server IP>`** with the public IP address of the EC2 instance I'm using as the Consul server.

---

### Setup Backend Servers

Since we have the Consul server up and running, let's manage our Nginx backend servers more easily using service discovery. To do this, we'll install Nginx and the Consul agent on all the backend servers. The Consul agent acts like a messenger, automatically registering both the server and the Nginx service running on it with the Consul server, which acts like a central directory.

**Apply the configurations below on both backend servers:**

- I SSH into the backend servers and ran **`sudo apt-get update -y`** to update package information.

![122](img/Screenshot%20(122).png)
![123](img/Screenshot%20(123).png)

> [!NOTE]
The `y` flag automatically answers **"yes"** to any prompts during the installation process, ensuring that the installation proceeds without requiring manual confirmation.

- I installedd Nginx on both instances by running the following command: **`sudo apt install nginx -y`**.

![124](img/Screenshot%20(124).png)
![125](img/Screenshot%20(125).png)

> [!NOTE]
After installing Nginx, I navigated to the default HTML directory and modified the index.html file on both servers to differentiate them.

- I navigated to the HTML directory by executing the following command: **`cd /var/www/html`**.

- I opened the HTML file with my preferred text editor to make edits: **`sudo vi index.html`**.

- I copied the HTML content below into the index.html file. On the second server, I replaced **SERVER-01** with **SERVER-02** in the HTML file to differentiate between the two backend servers.

```
<!DOCTYPE html>
<html>
<head>
	<title>Kanekis Backend Server </title>
</head>
<body>
	<h1>This is Backend SERVER-01</h1>
</body>
</html>
```

![126](img/Screenshot%20(126).png)

- I installed Consul as an agent on the servers. I ran the following commands to install Consul:

```
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update && sudo apt install consul
```


- I verified that Consul is installed properly by running the following command: **`consul --version`**.

![129](img/Screenshot%20(129).png)

- I replaced the default Consul configuration file **`config.hcl`** located in **`/etc/consul.d`** with my custom **`consul.hcl`** file.

- I renamed the default file and created a new one by running the following commands:

```
sudo mv /etc/consul.d/consul.hcl /etc/consul.d/consul.hcl.back
sudo vi /etc/consul.d/consul.hcl
```

- I added the following contents to the file. i replaced **`<YOUR_ENCRYPTED_KEY>`①** with my encryption key. Also, I replaced **`34.201.77.72`②** with my Consul server's IP address.

``` 
"server" = false
"datacenter" = "dc1"
"data_dir" = "/var/consul"
"encrypt" = "<YOUR_ENCRYPTED_KEY>"
"log_level" = "INFO"
"enable_script_checks" = true
"enable_syslog" = true
"leave_on_terminate" = true
"start_join" = ["34.201.77.72"]
```

![130](img/Screenshot%20(130).png)

---

**Here's an explanation of the Consul agent configuration settings:**

1. **`server = false`**: Indicates that this node is not a Consul server, but a client (agent). A Consul server handles requests from other Consul agents, while a client node registers services and performs checks.

2. **`datacenter = "dc1"`**: Specifies the datacenter name where the Consul agent operates. This should match the datacenter configuration on the Consul server to ensure proper communication.

3. **`data_dir = "/var/consul"`**: Defines the directory where the Consul agent will store its data files. This directory must be writable by the Consul agent process.

4. **`encrypt = "<YOUR_ENCRYPTED_KEY>"`**: Provides the encryption key for securing communication between Consul agents and the Consul server. Replace **`<YOUR_ENCRYPTED_KEY>`** with the actual key generated using **`consul keygen`**.

5. **`log_level = "INFO"`**: Sets the verbosity of the log output. **`INFO`** level provides a balance between detail and readability, showing general information about Consul operations.

6. **`enable_script_checks = true`**: Enables the execution of script-based health checks. When set to **`true`**, the Consul agent can run custom scripts to check service health.

7. **`enable_syslog = true`**: Allows logging of Consul messages to the syslog service. When enabled, logs will be sent to the system's logging facility, which can be useful for centralized logging and monitoring.

8. **`leave_on_terminate = true`**: Ensures that the Consul agent will automatically deregister itself from the Consul server when the agent process is terminated. This helps maintain accurate service registration and avoids stale entries.

9. **`start_join = ["34.201.77.72"]`**: Lists the addresses of Consul servers or agents that this Consul client should contact when starting up to join the Consul cluster. Replace **`34.201.77.72`** with the IP address of your Consul server. This setting helps the agent locate and connect to the Consul server to begin registering services.

---

- Next, we need to create a **`backend.hcl`** configuration file in the **`/etc/consul.d`** directory to register the Nginx service and its health check URLs with the Consul server. This will enable the Consul server to continuously monitor the health of the Nginx service. I used the following command to create and edit the file: **`sudo vi /etc/consul.d/backend.hcl`**.

- I added the following contents to the **`backend.hcl`** file and saved it.

```
"service" = {
  "Name" = "backend"
  "Port" = 80
  "check" = {
    "args" = ["curl", "localhost"]
    "interval" = "3s"
  }
}
```

![132](img/Screenshot%20(132).png)
![133](img/Screenshot%20(133).png)

This configuration registers my backend servers with the Consul server and sets up a health check that uses curl to test the service every 3 seconds.

- I verified the configurations by executing the following command: **`consul validate /etc/consul.d`**.

![134](img/Screenshot%20(134).png)
![135](img/Screenshot%20(135).png)

- Once all configurations are complete, I started the Consul agent with the following command: **`sudo nohup consul agent -config-dir /etc/consul.d/ &`**.

![136](img/Screenshot%20(136).png)
![137](img/Screenshot%20(137).png)

- To verify if everything is working correctly, visit your Consul UI. If you see the backend listed in the UI as depicted below, it indicates that the backend has successfully registered itself with Consul.

![138](img/Screenshot%20(138).png)

![139](img/Screenshot%20(139).png)


---

### Setup Load-Balancer

Next, I set up the load balancer to automatically update its backend server information based on the service registry maintained by Consul.
To retrieve the backend server details, I used the **`consul-template`** binary. This tool interacts with the Consul server via API calls to fetch the backend server information. It then uses a template to substitute values and generate the **`loadbalancer.conf`** file, which is utilized by Nginx.

- I logged in to the load-balancer server. Updated the package information and installed unzip with the following commands:

```
sudo apt-get update -y
sudo apt-get install unzip -y
```

- I installed Nginx using the following command: **`sudo apt install nginx -y`**.

![141](img/Screenshot%20(141).png)

- I downloaded the consul-template binary using the following command:

```
sudo curl -L  https://releases.hashicorp.com/consul-template/0.30.0/consul-template_0.30.0_linux_amd64.zip -o /opt/consul-template.zip

sudo unzip /opt/consul-template.zip -d  /usr/local/bin/
```


- To verify the installation of consul-template, I checked its version with the following command: **`consul-template --version`**.


- I created and edited a file named **`load-balancer.conf.ctmpl`** in the **`/etc/nginx/conf.d`** directory, using the following command: **`sudo vi /etc/nginx/conf.d/load-balancer.conf.ctmpl`**.

- I pasted the following content into the file:

```
upstream backend {
 {{- range service "backend" }} 
  server {{ .Address }}:{{ .Port }}; 
 {{- end }} 
}

server {
   listen 80;

   location / {
      proxy_pass http://backend;
   }
}
```


---

**Here's a breakdown of the configuration:**

1. **Upstream Block**

```
upstream backend {
 {{- range service "backend" }} 
  server {{ .Address }}:{{ .Port }}; 
 {{- end }} 
}
```

- **upstream backend:** This defines a group of backend servers that Nginx can load-balance requests across.
- **{{- range service "backend" }}:** This is a Consul-Template directive that iterates over all services registered with Consul under the name "backend".
- **server {{ .Address }}:{{ .Port }};:** For each backend service, it adds an entry to the upstream block with the server's address and port.
- **{{- end }}:** Ends the iteration block.

2. **Server Block**

```
server {
   listen 80;

   location / {
      proxy_pass http://backend;
   }
}

```

- **server:** Defines a virtual server that listens for incoming requests.
- **listen 80:** Specifies that this server block will listen on port 80 (the default HTTP port).
- **location /:** Defines a location block for all requests to the root URL (/).
- **proxy_pass http://backend:** Forwards incoming requests to the upstream group named "backend" defined above. Nginx will use the addresses and ports listed in the upstream backend block to balance the requests.

> [!NOTE]
This setup keeps Nginx's backend server list in sync with Consul's. It ensures that Nginx always routes traffic to the currently available backend servers.

---

- I created a file named **`consul-template.hcl`** in the **`/etc/nginx/conf.d/`** directory. This configuration file is used by **consul-template** to specify details about the Consul server IP and the destination path where the processed load-balancer.conf file will be saved.

I used the following command to create and edit the file: **`sudo vi /etc/nginx/conf.d/consul-template.hcl`**.

- I added the following content to the file, replacing **`<Consul Server IP>`** with my Consul server's IP address. This configuration specifies the Consul server details, the path to the template file, the destination for the rendered Nginx configuration, and the command to reload Nginx after updating the configuration.

```
consul {
 address = "<Consul Server IP>:8500"

 retry {
   enabled  = true
   attempts = 12
   backoff  = "250ms"
 }
}
template {
 source      = "/etc/nginx/conf.d/load-balancer.conf.ctmpl"
 destination = "/etc/nginx/conf.d/load-balancer.conf"
 perms       = 0600
 command = "service nginx reload"
}
```


- I deleted the default server configuration to disable it by running the following command: **`sudo rm /etc/nginx/sites-enabled/default`**.

![146](img/Screenshot%20(146).png)

*The default server configuration file should be deleted to avoid inconsistencies with the server's settings.*

- I restarted Nginx to apply the changes by running the following command: **`sudo systemctl restart nginx`**.

- Once configurations are complete, I started the Consul Template agent using the following command. It continuously monitors Consul for changes.

```
sudo nohup consul-template -config=/etc/nginx/conf.d/consul-template.hcl &
```

- Upon completion, a load-balancer.conf file will be created with backend server information populated from the Consul service registry.

![147](img/Screenshot%20(147).png)

Now, I accessed the load balancer IP in your web browser, it displayed the custom HTML content from one of the backend servers. When I refreshed the page, the load balancer routed my request to the other backend server, displaying its custom HTML content.

![148](img/Screenshot%20(148).png)

![149](img/Screenshot%20(149).png)

This behavior occurs because the load balancer uses a round-robin algorithm by default, distributing incoming requests evenly across all available backend servers.

---

### Service Discovery Test

Now that everything is set up and running, you can test the configuration by observing what happens when you stop one of your backend servers.


Stop one of the backend servers. The Consul server will monitor the health of each registered service. Once a backend server is stopped, Consul will detect the server's unavailability and mark it as unhealthy. The health check for that server will fail, and it will be removed from the load balancer's active pool of servers.

![150](img/Screenshot%20(150).png)

As a result, the load balancer will only direct traffic to the remaining healthy backend servers. This ensures that your application continues to run smoothly without any disruption to users, demonstrating the effectiveness of your service discovery and health check configuration with Consul and Nginx.

![sd_test](img/full_sd_test.gif)

**Service Check:** These checks are specific to the services running on the nodes (in this case, Nginx). When you stopped Nginx, the service check that monitors the health of Nginx on that particular node would fail, leading to the "all service checks failed" error.

**Node checks:** These checks monitor the overall health of the node itself, which includes the underlying operating system and possibly other metrics (like CPU, memory, and disk usage). Since stopping Nginx does not necessarily mean the node is unhealthy (the node could still be up and running, responding to pings, etc.), the node checks would still pass.

---
---

#### The End Of Project 5