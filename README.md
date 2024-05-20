# Google Cloud Computing Foundations
Repository of my study from [Gooogle Cloud Computing Foundations Certificate](https://www.cloudskillsboost.google/paths/36) & [Google Cloud Essentials](https://www.cloudskillsboost.google/course_templates/621).

> [!Important]
> Copyright 2024 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.



# Table of contents
- [Cloud Shell](#cloud-shell-_)
  - [Regions and Zones](#regions-and-zones)
- [1. Virtual Machine](#1-create-a-virtual-machine)
  - [1.1 Windows Server](#11-remote-desktop-rdp-into-the-windows-server)
  - [1.2 SSH Conection](#12-use-ssh-to-connect-to-your-instance) 
- [2. NGINX Web Server](#2-create-a-nginx-web-server)
  - [2.1 Update the Firewall](#21-update-the-firewall)
- [3. Kubernetes Engine](#3-kubernetes-engine)
  - [3.1 Deploy an application](#31-deploy-an-application-to-the-cluster)
- [4. Set Up Network and HTTP Load Balancers](4-set-up-network-and-http-load-balancers)
  - [4.1 Create multiple web server instances](#41-create-multiple-web-server-instances)
  - [4.2 Configure the load balancing service](#42-configure-the-load-balancing-service)
  - [4.3 Sending traffic to your instances](#43-sending-traffic-to-your-instances)
  - [4.4 Create an HTTP load balancer](#44-create-an-http-load-balancer)
  - [4.5 Testing traffic sent to your instances](#45-testing-traffic-sent-to-your-instances)
- [5. App Engine with Python](#5-app-engine-with-python)
  - [5.1 Enable Google App Engine Admin API](#51-enable-google-app-engine-admin-api)
  - [5.2 Download the Hello World app](#52-download-the-hello-world-app)
  - [5.3 Test the application](#53-test-the-application)
  - [5.4 Make a change](#54-make-a-change)
  - [5.5 Deploy your App](#55-deploy-your-app)
- [6. Cloud Functions](#6-cloud-functions)
  - [6.1 Create a function](#61-create-a-function)
  - [6.2 Create a cloud storage bucket](#62-create-a-cloud-storage-bucket)
  - [6.3 Deploy your function](#63-deploy-your-function)
  - [6.4 Test the function](#64-test-the-function)
  - [6.5 View logs](#65-view-logs)
- [7. Cloud Storage: CLI/SDK](#7-cloud-storage)
  - [7.1 Create a Bucket](#71-create-a-bucket)
  - [7.2 Upload an object into your bucket](#72-upload-an-object-into-your-bucket)
  - [7.3 Download an object from your bucket](#73-download-an-object-from-your-bucket)
  - [7.4 Copy an object to a folder in the bucket](#74-copy-an-object-to-a-folder-in-the-bucket)
  - [7.5 List contents of a bucket or folder](#75-list-contents-of-a-bucket-or-folder)
  - [7.6 List details for an object](#76-list-details-for-an-object)
  - [7.7 Make your object publicly accessible](#77-make-your-object-publicly-accessible)
  - [7.8 Remove public access](#78-remove-public-access)


## Cloud Shell `>_`
Cloud Shell is a **Debian-based** virtual machine with a persistent 5-GB home directory, which makes it easy for you to manage your Google Cloud projects and resources. 
### Update the OS
```
sudo apt-get update
```

### Table format in Cloud Shell
```
gcloud config set accessibility/screen_reader False
```
> For more information see [Enabling accessibility features](https://cloud.google.com/sdk/docs/enabling-accessibility-features).

### List the active account name
```shell
gcloud auth list
```

<details>
<summary>Expected Output</summary> 
  
```shell
ACTIVE: *
ACCOUNT: "ACCOUNT"

To set the active account, run:
    $ gcloud config set account `ACCOUNT`
```
</details>

### List the project ID
```shell
gcloud config list project
```

<details>
<summary>Expected Output</summary> 
  
```shell
[core]
project = "PROJECT_ID"
```
</details>

### View the project ID
```shell
gcloud config get-value project
```

### View details about the project
```shell
gcloud compute project-info describe --project $(gcloud config get-value project)
```

> [!Note]
> When the `google-compute-default-region` and `google-compute-default-zone` keys and values are missing from the output, no default zone or region is set.

### View the list of configurations in your environment
```shell
gcloud config list
```
### To see all properties and their settings
```shell
gcloud config list --all
```
### List your components
```shell
gcloud components list
```

### Filtering
```shell
gcloud compute instances list --filter="name=('INSTANCE_NAME')"
```

### List the firewall rules in the project
```shell
gcloud compute firewall-rules list

```

### List the firewall rules for the default network
```shell
gcloud compute firewall-rules list --filter="network='default'"
```

### List the firewall rules for the default network where the allow rule matches an ICMP rule
```shell
gcloud compute firewall-rules list --filter="NETWORK:'default' AND ALLOW:'icmp'"
```

### View the available logs on the system
```shell
gcloud logging logs list
```

### View the logs that relate to compute resources
```shell
gcloud logging logs list --filter="compute"
```

### Read the logs related to the resource type of gce_instance
```shell
gcloud logging read "resource.type=gce_instance" --limit 5
```

### Read the logs for a specific virtual machine
```shell
gcloud logging read "resource.type=gce_instance AND labels.instance_name='<INSTANCE_NAME>'" --limit 5
```




### Regions and Zones
Regions | Zones
--- | ---
Regions are collections of zones. | A zone is a deployment area within a region.
Zones have high-bandwidth, low-latency network connections to other zones in the same region. | The fully-qualified name for a zone is made up of `<region>-<zone>`.

### Set the project region by default
```shell
gcloud config set compute/region <REGION>
```
### Set the project zone by default
```shell
gcloud config set compute/zone <ZONE>
```
> After set the default region and zone, you don't have to append the `--zone` flag every time.

### Create a variable for region
```shell
export REGION=<REGION>
```

### Create a variable for zone
```shell
export ZONE=<ZONE>
export ZONE=$(gcloud config get-value compute/zone)
```

### Create an enviroment variable for store your PROJECTID
```shell
export PROJECT_ID=$(gcloud config get-value project)
```

> To use the variable: `$VARIABLE`

### To verify that your variables were set properly
```shell
echo -e "PROJECT ID: $PROJECT_ID\nZONE: $ZONE"
```

> [!Note]
> When you run gcloud on your own machine, the config settings are persisted across sessions. But in Cloud Shell, you need to set this for every new session or reconnection.

## 1. Create a Virtual Machine
Compute Engine allows you to create virtual machines (VMs) that run different operating systems, including multiple flavors of Linux (Debian, Ubuntu, Suse, Red Hat, CoreOS) and Windows Server, on Google infrastructure.
### Create a new VM instance
```shell
gcloud compute instances create <INSTANCE_NAME> --machine-type <MACHINE_TYPE> --zone=$ZONE
```

<details>
<summary>Expected Output</summary> 
  
```shell
Created [..."INSTANCE_NAME"].
     NAME: "INSTANCE_NAME"
     ZONE:  "ZONE"
     MACHINE_TYPE: "MACHINE_TYPE"
     PREEMPTIBLE:
     INTERNAL_IP: 10.128.0.3
     EXTERNAL_IP: 34.136.51.150
     STATUS: RUNNING
```
</details>

<details>
<summary>Where</summary> 
  
`gcloud compute` allows you to manage your Compute Engine resources in a format that's simpler than the Compute Engine API.

`instances create` creates a new instance.

The `--machine-type` flag specifies the machine type.

The `--zone` flag specifies where the VM is created.

If you omit the `--zone` flag, the gcloud tool can infer your desired zone based on your default properties. Other required instance settings, such as machine type and image, are set to default values if not specified in the create command.

</details>

### List the compute instance available in the project
```shell
gcloud compute instances list
```


### To see all defaults
```shell
gcloud compute instances create --help
```
> Press Enter or the spacebar to scroll through the help content.
> 
> To exit the content, type Q.
> 
> To exit help, press **CTRL + C**.

## 1.1 Remote Desktop (RDP) into the Windows Server
To create a Windows Server, follow these steps while creating a Virtual Machine in
- Cloud Console:
1. In the **Boot disk** section, click **Change** to begin configuring your boot disk.
2. Under **Operating system** select **Windows Server**
- Cloud Shell
```shell
gcloud compute instances create <INSTANCE_NAME> \
    --image-project windows-cloud \
    --image-family <IMAGE_FAMILY> \
    --machine-type <MACHINE_TYPE> \
    --boot-disk-size <BOOT_DISK_SIZE> \
    --boot-disk-type <[BOOT_DISK_TYPE>
```
<details>
  <summary>Where</summary>

`<INSTANCE_NAME>` is the name for the new instance.

`<IMAGE_FAMILY>` is one of the public image families for Windows Server images.

`<MACHINE_TYPE>` is one of the available machine types.

`<BOOT_DISK_SIZE>` is the size of the boot disk in GB. Larger persistent disks have higher throughput.

`<BOOT_DISK_TYPE>` is the type of the boot disk for your instance. For example, pd-ssd.

</details>

### To see whether the server instance is ready for an RDP connection
```shell
gcloud compute instances get-serial-port-output <INSTANCE_NAME> --zone=$ZONE
```
> If prompted, type `N` and press **ENTER**.
> 
> Repeat the command until you see the following in the command output: `Instance setup finished. instance is ready to use.`

### To set a password for logging into the RDP
```shell
gcloud compute reset-windows-password <INSTANCE_NAME> --zone $ZONE --user <USERNAME>
```
> If asked `Would you like to set or reset the password for [admin] (Y/n)?`, enter `Y`.
> 
>  Record the password for use in later steps to connect.

### Connect to your server
Through an RDP app already installed on your computer (Enter the external IP of your VM) or through RDP directly from the **Chrome** browser using the **Spark View** extension (Use your Windows username admin and password you previously recorded). 

## 1.2 Use SSH to connect to your instance
```shell
gcloud compute ssh <INSTANCE_NAME> --zone=$ZONE
```
> When prompted `Do you want to continue? (Y/n)` type `Y`.
>
> Disconnect from SSH by exiting from the remote shell: `exit`.
> 
> To leave the passphrase empty, press **Enter** twice.

> [!Note]
> You have connected to a virtual machine and notice how the command prompt changed?
> 
> The prompt now says something similar to `sa_107021519685252337470@<INSTANCE_NAME>`.
> 
> The reference before the `@` indicates the account being used.
> 
> After the `@` sign indicates the host machine being accessed.
>
> `ssh username@hostname`

## 2. Create a NGINX Web Server
### Install NGINX

```shell
sudo apt-get install -y nginx
```
<details>
  <summary>Expected Output</summary>
  
 ```shell
  Reading package lists... Done
  Building dependency tree
  Reading state information... Done
  The following additional packages will be installed:
  ...
  ```
</details>

### Confirm that NGINX is running
```shell
ps auwx | grep nginx
```

<details>
  <summary>Expected Output</summary>
  
```shell
root      2330  0.0  0.0 159532  1628 ?        Ss   14:06   0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
www-data  2331  0.0  0.0 159864  3204 ?        S    14:06   0:00 nginx: worker process
www-data  2332  0.0  0.0 159864  3204 ?        S    14:06   0:00 nginx: worker process
root      2342  0.0  0.0  12780   988 pts/0    S+   14:07   0:00 grep nginx
```
</details>

### To see the web page
```shell
http://EXTERNAL_IP/
```

## 2.1 Update the Firewall
### List the firewall rules for the project
```shell
gcloud compute firewall-rules list
```

> [!Note]
> Communication with the virtual machine will fail as it does not have an appropriate firewall rule. The **nginx** web server is expecting to communicate on **tcp:80**. To get communication working you need to:
> 
> 1. Add a tag to the virtual machine
>
> 2. Add a firewall rule for http traffic

### Add a tag to the virtual machine
```shell
gcloud compute instances add-tags <INSTANCE_NAME> --tags http-server,https-server
```

### Update the firewall rule to allow
```shell
gcloud compute firewall-rules create <default-allow-http> --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server
```

### List the firewall rules for the project
```shell
gcloud compute firewall-rules list --filter=ALLOW:'80'
```

<details>
  <summary>Expected Output</summary>
  
  ```shell
NAME                  NETWORK  DIRECTION  PRIORITY  ALLOW   DENY  DISABLED
<default-allow-http>  default  INGRESS    1000      tcp:80        False
  ```
</details>

### Verify communication is possible for http to the virtual machine
```shell
curl http://$(gcloud compute instances list --filter=name:<INSTANCE_NAME> --format='value(<EXTERNAL_IP>)')
```
> You will see the default **nginx** output.

## 3. Kubernetes Engine
**Google Kubernetes Engine (GKE)** provides a managed environment for deploying, managing, and scaling your containerized applications using Google infrastructure. The GKE environment consists of multiple machines (specifically Compute Engine instances) grouped to form a container cluster.
### Create a GKE cluster
```shell
gcloud container clusters create --machine-type=<MACHINE_TYPE> --zone=$ZONE <CLUSTER_NAME>
```
> A cluster consists of at least one cluster master machine and multiple worker machines called nodes. Nodes are Compute Engine virtual machine (VM) instances that run the Kubernetes processes necessary to make them part of the cluster.
>
> 
> You can ignore any warnings in the output. It might take several minutes to finish creating the cluster.

> [!Note]
>  Cluster names must start with a letter and end with an alphanumeric, and cannot be longer than 40 characters.

<details>
  <summary>Expected Output</summary>

```shell
NAME: <CLUSTER_NAME>
LOCATION: <ZONE>
MASTER_VERSION: 1.22.8-gke.202
MASTER_IP: 34.67.240.12
MACHINE_TYPE: <MACHINE_TYPE>
NODE_VERSION: 1.22.8-gke.202
NUM_NODES: 3
STATUS: RUNNING
```
</details>

### Authenticate with the cluster
```shell
gcloud container clusters get-credentials lab-cluster
```
> After creating your cluster, you need authentication credentials to interact with it.

<details>
  <summary>Expected Output</summary>

```shell
Fetching cluster endpoint and auth data.
kubeconfig entry generated for my-cluster.
```
</details>

## 3.1 Deploy an application to the cluster
GKE uses Kubernetes objects to create and manage your cluster's resources. Kubernetes provides the Deployment object for deploying stateless applications like web servers. Service objects define rules and load balancing for accessing your application from the internet.

### To create a new Deployment `hello-server` from the `hello-app` container image
```shell
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```

<details>
  <summary>Expected Output</summary>

```shell
deployment.apps/hello-server created
```
</details>

> This Kubernetes command creates a deployment object that represents `hello-server`.
> 
> In this case, `--image` specifies a container image to deploy. The command pulls the example image from a Container Registry bucket.
> 
> `gcr.io/google-samples/hello-app:1.0` indicates the specific image version to pull. If a version is not specified, the latest version is used.


### To create a Kubernetes Service
> This is a Kubernetes resource that lets you expose your application to external traffic

```shell
kubectl expose deployment hello-server --type=LoadBalancer --port 8080
```

<details>
  <summary>Expected Output</summary>

```shell
service/hello-server exposed
```
</details>

<details>
  <summary>Where</summary>

`--port` specifies the port that the container exposes.

`type="LoadBalancer"` creates a Compute Engine load balancer for your container.
</details>

### To inspect the hello-server Service
```shell
kubectl get service
```
<details>
  <summary>Expected Output</summary>

```shell
NAME             TYPE            CLUSTER-IP      EXTERNAL-IP     PORT(S)           AGE
hello-server     loadBalancer    10.39.244.36    35.202.234.26   8080:31991/TCP    65s
kubernetes       ClusterIP       10.39.240.1               433/TCP           5m13s
```
</details>

> [!Note]
> It might take a minute for an external IP address to be generated.
>
> Run the previous command again if the **EXTERNAL-IP** column status is pending.

### To view the application from your web browser
> Open a new tab and enter the following address, replacing `<EXTERNAL-IP>` with the EXTERNAL-IP for hello-server.
```shell
http://<EXTERNAL-IP>:8080
```

<details>
  <summary>Expected Output</summary>

The browser tab displays the message **Hello, world!** as well as the version and hostname.
</details>

### To delete the cluster
```shell
gcloud container clusters delete lab-cluster
```
> When prompted, type `Y` to confirm.
>
> Deleting the cluster can take a few minutes.

## 4. Set Up Network and HTTP Load Balancers
There are several ways you can load balance on Google Cloud. Here you will set up the following load balancers:
- **Network Load Balancer**
- **HTTP(s) Load Balancer**

## 4.1 Create multiple web server instances
### Create a virtual machine `<www1>` in your default zone
```shell
gcloud compute instances create <www1> \
  --zone=$ZONE \
  --tags=network-lb-tag \
  --machine-type=e2-small \
  --image-family=debian-11 \
  --image-project=debian-cloud \
  --metadata=startup-script='#!/bin/bash
    apt-get update
    apt-get install apache2 -y
    service apache2 restart
    echo "
<h3>Web Server: <www1></h3>" | tee /var/www/html/index.html'
```
> For this load balancing scenario, three Compute Engine VM instances were created, and **Apache** was installed on each of them.

### Create a firewall rule to allow external traffic to the VM instances
```shell
gcloud compute firewall-rules create <www-firewall-network-lb> \
    --target-tags network-lb-tag --allow tcp:80
```
> Now you need to get the external IP addresses of your instances and verify that they are running.

### Run the following to list your instances
```shell
gcloud compute instances list
```
### Verify that each instance is running 
```shell
curl http://<IP_ADDRESS>
```
> Replace <IP_ADDRESS> with the IP address for each of your VMs.

## 4.2 Configure the load balancing service
When you configure the load balancing service, your virtual machine instances receives packets that are destined for the static external IP address you configure. Instances made with a Compute Engine image are automatically configured to handle this IP address.
> [!Note]
> Learn more about how to set up network load balancing from the [External TCP/UDP Network Load Balancing overview Guide](https://cloud.google.com/compute/docs/load-balancing/network/).

### Create a static external IP address for your load balancer
```shell
gcloud compute addresses create <network-lb-ip-1> \
  --region $REGION
```
<details>
  <summary>Expected Output</summary>

```shell
Created [https://www.googleapis.com/compute/v1/projects/qwiklabs-gcp-03-xxxxxxxxxxx/regions//addresses/network-lb-ip-1].
```
  
</details>

 ### Add a legacy HTTP health check resource
 ```shell
gcloud compute http-health-checks create <basic-check>
```

### To create the target pool and use the health check, which is required for the service to function
```shell
gcloud compute target-pools create <www-pool> \
  --region $REGION --http-health-check <basic-check>
```

### Add the instances to the pool:
```shell
gcloud compute target-pools add-instances <www-pool> \
    --instances <www1>,<www2>,<www3>
```

### Add a forwarding rule
```shell
gcloud compute forwarding-rules create <www-rule> \
    --region  $REGION \
    --ports 80 \
    --address <network-lb-ip-1> \
    --target-pool <www-pool>
```

## 4.3 Sending traffic to your instances

### To view the external IP address of the `<www-rule>` forwarding rule used by the load balancer
```shell
gcloud compute forwarding-rules describe <www-rule> --region $REGION
```

### Access the external IP address
```shell
IPADDRESS=$(gcloud compute forwarding-rules describe <www-rule> --region $REGION --format="json" | jq -r .IPAddress)
```

### Show the external IP address
```shell
echo $IPADDRESS
```

### To access the external IP address
```shell
while true; do curl -m1 $IPADDRESS; done
```
>  Replacing `IP_ADDRESS` with an external IP address from the previous command
> 
> Use Ctrl + C to stop running the command.

> [!Note]
> The response from the `curl` command alternates randomly among the three instances. If your response is initially unsuccessful, wait approximately 30 seconds for the configuration to be fully loaded and for your instances to be marked healthy before trying again.


## 4.4 Create an HTTP load balancer
HTTP(S) Load Balancing is implemented on Google Front End (GFE). GFEs are distributed globally and operate together using Google's global network and control plane. You can configure URL rules to route some URLs to one set of instances and route other URLs to other instances.

Requests are always routed to the instance group that is closest to the user, if that group has enough capacity and is appropriate for the request. If the closest group does not have enough capacity, the request is sent to the closest group that does have capacity.

> [!IMPORTANT]
> To set up a load balancer with a Compute Engine backend, your VMs need to be in an instance group. The managed instance group provides VMs running the backend servers of an external HTTP load balancer.

### To create the load balancer template
```shell
gcloud compute instance-templates create <lb-backend-template> \
   --region=REGION \
   --network=default \
   --subnet=default \
   --tags=allow-health-check \
   --machine-type=e2-medium \
   --image-family=debian-11 \
   --image-project=debian-cloud \
   --metadata=startup-script='#!/bin/bash
     apt-get update
     apt-get install apache2 -y
     a2ensite default-ssl
     a2enmod ssl
     vm_hostname="$(curl -H "Metadata-Flavor:Google" \
     http://169.254.169.254/computeMetadata/v1/instance/name)"
     echo "Page served from: $vm_hostname" | \
     tee /var/www/html/index.html
     systemctl restart apache2'
```
> **Managed instance groups (MIGs)** let you operate apps on multiple identical VMs.
>
> You can make your workloads scalable and highly available by taking advantage of automated MIG services, including: autoscaling, autohealing, regional (multiple zone) deployment, and automatic updating.

### Create a managed instance group based on the template
```shell
gcloud compute instance-groups managed create <lb-backend-group> \
   --template=<lb-backend-template> --size=2 --zone=ZONE
```

### Create the `<fw-allow-health-check firewall>` rule
```shell
gcloud compute firewall-rules create <fw-allow-health-check> \
  --network=default \
  --action=allow \
  --direction=ingress \
  --source-ranges=130.211.0.0/22,35.191.0.0/16 \
  --target-tags=allow-health-check \
  --rules=tcp:80
```
> [!Note]
> The ingress rule allows traffic from the Google Cloud health checking systems (130.211.0.0/22 and 35.191.0.0/16).

### To set up a global static external IP address that your customers use to reach your load balancer
```shell
gcloud compute addresses create <lb-ipv4-1> \
  --ip-version=IPV4 \
  --global
```

### Note the IPv4 address that was reserved
```shell
gcloud compute addresses describe <lb-ipv4-1> \
  --format="get(address)" \
  --global
```

### Create a health check for the load balancer
```shell
gcloud compute health-checks create <http http-basic-check> \
  --port 80
```

> [!Note]
> Google Cloud provides health checking mechanisms that determine whether backend instances respond properly to traffic.
>  For more information, please refer to the [Creating health checks document.](https://cloud.google.com/load-balancing/docs/health-checks)

### Create a backend service:
```shell
gcloud compute backend-services create <web-backend-service> \
  --protocol=HTTP \
  --port-name=http \
  --health-checks=<http-basic-check> \
  --global
```

### Add your instance group as the backend to the backend service
```shell
gcloud compute backend-services add-backend <web-backend-service> \
  --instance-group=<lb-backend-group> \
  --instance-group-zone=ZONE \
  --global
```

### Create a URL map to route the incoming requests to the default backend service
```shell
gcloud compute url-maps create <web-map-http> \
    --default-service <web-backend-service>
```

> [!Note]
> URL map is a Google Cloud configuration resource used to route requests to backend services or backend buckets.
>
> For example, with an external HTTP(S) load balancer, you can use a single URL map to route requests to different destinations based on the rules configured in the URL map:
> - Requests for https://example.com/video go to one backend service.
>
> - Requests for https://example.com/audio go to a different backend service.
>
> - Requests for https://example.com/images go to a Cloud Storage backend bucket.
>
> - Requests for any other host and path combination go to a default backend service.
>

### Create a target HTTP proxy to route requests to your URL map
```shell
gcloud compute target-http-proxies create <http-lb-proxy> \
    --url-map <web-map-http>
```

### Create a global forwarding rule to route incoming requests to the proxy
```shell
gcloud compute forwarding-rules create <http-content-rule> \
   --address=<lb-ipv4-1>\
   --global \
   --target-http-proxy=<http-lb-proxy> \
   --ports=80
```

> [!Note]
> A forwarding rule and its corresponding IP address represent the frontend configuration of a Google Cloud load balancer. Learn more about the general understanding of forwarding rules from the [Forwarding rule overview Guide.](https://cloud.google.com/load-balancing/docs/forwarding-rule-concepts)

## 4.5 Testing traffic sent to your instances
1. In the Google Cloud console, from the **Navigation menu**, go to **Network services** > **Load balancing**.
2. Click on the load balancer that you just created (`web-map-http`).
3. In the Backend section, click on the name of the backend and confirm that the VMs are Healthy. If they are not healthy, wait a few moments and try reloading the page.
4. When the VMs are healthy, test the load balancer using a web browser, going to `http://IP_ADDRESS/`, replacing `IP_ADDRESS` with the load balancer's IP address.

> This may take three to five minutes. If you do not connect, wait a minute, and then reload the browser.
>
> Your browser should render a page with content showing the name of the instance that served the page, along with its zone (for example, Page served from: `lb-backend-group-xxxx`).

## 5. App Engine with python
App Engine allows developers to focus on doing what they do best, writing code, and not what it runs on. The notion of servers, virtual machines, and instances have been abstracted away, with App Engine providing all the compute necessary. Developers don't have to worry about operating systems, web servers, logging, monitoring, load-balancing, system administration, or scaling, as App Engine takes care of all that. 

## 5.1 Enable Google App Engine Admin API
The App Engine Admin API enables developers to provision and manage their App Engine Applications.
### In the Cloud Console
1. In the left Navigation menu, click **APIs & Services** > **Library**.
2. Type **"App Engine Admin API"** in the search box.
3. Click the App Engine Admin API card.
   
> If there is no prompt to enable the API, then it is already enabled and no action is needed.

## 5.2 Download the Hello World app
There is a simple Hello World app for Python you can use to quickly get a feel for deploying an app to Google Cloud.
### To copy the Hello World sample app repository to your Google Cloud instance
```shell
git clone https://github.com/GoogleCloudPlatform/python-docs-samples.git
```
### Go to the directory that contains the sample code
```shell
cd python-docs-samples/appengine/standard_python3/hello_world
```

## 5.3 Test the application
Test the application using the Google Cloud development server (dev_appserver.py), which is included with the preinstalled App Engine SDK.

### Start the Google Cloud development server
> From within your helloworld directory where the app's app.yaml configuration file is located
```shell
dev_appserver.py app.yaml
```
> The development server is now running and listening for requests on port 8080.
>
> View the results by clicking the **Web preview** > **Preview on port 8080**.
>
> You'll see a **"Hello World!"** in a new browser window.

## 5.4 Make a change
You can leave the development server running while you develop your application. The development server watches for changes in your source files and reloads them if necessary.
### Leave the development server running
### Click the (**+**) next to your Cloud Shell tab to open a new command line session
### To go to the directory that contains the sample code
```shell
cd python-docs-samples/appengine/standard_python3/hello_world
```
### To open main.py in nano to edit the content
```shell
nano main.py
```
> Change **"Hello World!"** to **"Hello, Cruel World!"**.
> Save the file with CTRL-S and exit with CTRL-X.
### Reload the **Hello World! Browser** or click the **Web Preview** > **Preview on port 8080** to see the results
> Browser window with **Hello, Cruel World!** on the page.

## 5.5 Deploy your app
### To deploy your app to App Engine
> From within the root directory of your application where the app.yaml file is located
```shell
gcloud app deploy
```

### Enter the number that represents your region
> The App Engine application will then be created.

<details>
  <summary>Expected Output</summary>

```shell
Creating App Engine application in project [qwiklabs-gcp-233dca09c0ab577b] and region ["REGION"]....done.
Services to deploy:

descriptor:      [/home/gcpstaging8134_student/python-docs-samples/appengine/standard/hello_world/app.yaml]
source:          [/home/gcpstaging8134_student/python-docs-samples/appengine/standard/hello_world]
target project:  [qwiklabs-gcp-233dca09c0ab577b]
target service:  [default]
target version:  [20171117t072143]
target url:      [https://qwiklabs-gcp-233dca09c0ab577b.appspot.com]

Do you want to continue (Y/n)?
```
</details>

> Enter Y when prompted to confirm the details and begin the deployment of service.

<details>
  <summary>Expected Output</summary>

```shell
Beginning deployment of service [default]...
Some files were skipped. Pass `--verbosity=info` to see which ones.
You may also view the gcloud log file, found at
[/tmp/tmp.dYC7xGu3oZ/logs/2017.11.17/07.18.27.372768.log].
╔════════════════════════════════════════════════════════════╗
╠═ Uploading 5 files to Google Cloud Storage                ═╣
╚════════════════════════════════════════════════════════════File upload done.
Updating service [default]...done.
Waiting for operation [apps/qwiklabs-gcp-233dca09c0ab577b/operations/2e88ab76-33dc-4aed-93c4-fdd944a95ccf] to complete...done.
Updating service [default]...done.
Deployed service [default] to [https://qwiklabs-gcp-233dca09c0ab577b.appspot.com]

You can stream logs from the command line by running:
  $ gcloud app logs tail -s default

To view your application in the web browser run:
  $ gcloud app browse
```
</details>

> [!Note]
> If you receive an error as "Unable to retrieve P4SA" while deploying the app, then re-run the above command.

## 5.6 View your application
### To launch your browser enter the following command
```shell
gcloud app browse
```
> Click on the link it provides:

<details>
  <summary>Expected Output</summary>

```shell
Did not detect your browser. Go to this link to view your app:
https://qwiklabs-gcp-233dca09c0ab577b.appspot.com
```
</details>

> Your application is deployed and you can read the short message in your browser.

## 6. Cloud Functions
A cloud function is a piece of code that runs in response to an event, such as an HTTP request, a message from a messaging service, or a file upload. Cloud events are things that happen in your cloud environment.

## 6.1 Create a function
> This function writes a message to the Cloud Functions logs.
>
> It is triggered by cloud function events and accepts a callback function used to signal completion of the function.
>
> The cloud function event is a cloud pub/sub topic event. A pub/sub is a messaging service where the senders of messages are decoupled from the receivers of messages. When a message is sent or posted, a subscription is required for a receiver to be alerted and receive the message. To learn more: [A Google-Scale Messaging Service.](https://cloud.google.com/pubsub/architecture)

### Set the default region
```shell
gcloud config set compute/region <REGION>
```

### Create a directory for the function code
```shell
mkdir gcf_hello_world
```

### Move to the gcf_hello_world directory
```shell
cd gcf_hello_world
```
### Create and open index.js to edit
```shell
nano index.js
```

### Copy the following into the index.js file
```javascript
/**
* Background Cloud Function to be triggered by Pub/Sub.
* This function is exported by index.js, and executed when
* the trigger topic receives a message.
*
* @param {object} data The event payload.
* @param {object} context The event metadata.
*/
exports.helloWorld = (data, context) => {
const pubSubMessage = data;
const name = pubSubMessage.data
    ? Buffer.from(pubSubMessage.data, 'base64').toString() : "Hello World";

console.log(`My Cloud Function: ${name}`);
};
```
> Exit nano (Ctrl+x) and save (Y) the file.

## 6.2 Create a Cloud Storage bucket
### To create a new Cloud Storage bucket
```shell
gsutil mb -p <PROJECT_ID> gs://<BUCKET_NAME>
```

## 6.3 Deploy your function
> When deploying a new function, you must specify `--trigger-topic`, `--trigger-bucket`, or `--trigger-http`. When deploying an update to an existing function, the function keeps the existing trigger unless otherwise specified.

### Disable the Cloud Functions API
```shell
gcloud services disable cloudfunctions.googleapis.com
```
### Re-enable the Cloud Functions API
```shell
gcloud services enable cloudfunctions.googleapis.com
```
### Add the `artifactregistry.reader` permission for your appspot service account
```shell
gcloud projects add-iam-policy-binding <PROJECT_ID> \
--member="serviceAccount:<PROJECT_ID>@appspot.gserviceaccount.com" \
--role="roles/artifactregistry.reader"
```

### Deploy the function to a pub/sub topic
```shell
gcloud functions deploy helloWorld \
  --stage-bucket <BUCKET_NAME> \
  --trigger-topic hello_world \
  --runtime nodejs20
```

> [!Note]
>  If you get OperationError, ignore the warning and re-run the command.

> If prompted, enter Y to allow unauthenticated invocations of a new function.

### Verify the status of the function
```shell
gcloud functions describe helloWorld
````
> An ACTIVE status indicates that the function has been deployed.
>
> Every message published in the topic triggers function execution, the message contents are passed as input data.


## 6.4 Test the function
After you deploy the function and know that it's active, test that the function writes a message to the cloud log after detecting an event.

### To create a message test of the function
```shell
DATA=$(printf 'Hello World!'|base64) && gcloud functions call helloWorld --data '{"data":"'$DATA'"}'
```
> The cloud tool returns the execution ID for the function, which means a message has been written in the log.

<details>
  <summary> Example Output</summary>
  
  ```shell
  executionId: 3zmhpf7l6j5b
  ```
</details>

> View logs to confirm that there are log messages with that execution ID.

### 6.5 View logs
### Check the logs to see your messages in the log history
```shell
gcloud functions logs read helloWorld
```
<details>
  <summary> Example Output</summary>
  
  ```shell
  LEVEL: D
  NAME: helloWorld
  EXECUTION_ID: 4bgl3jw2a9i3
  TIME_UTC: 2023-03-23 13:45:31.545
  LOG: Function execution took 912 ms, finished with status: 'ok'
   
  LEVEL: I
  NAME: helloWorld
  EXECUTION_ID: 4bgl3jw2a9i3
  TIME_UTC: 2023-03-23 13:45:31.533
  LOG: My Cloud Function: Hello World!
   
  LEVEL: D
  NAME: helloWorld
  EXECUTION_ID: 4bgl3jw2a9i3
  TIME_UTC: 2023-03-23 13:45:30.633
  LOG: Function execution started
  ```
</details>

> [!Note]
> The logs will take around 10 mins to appear. Also, the alternative way to view the logs is, go to Logging > Logs Explorer.

> Your application is deployed, tested, and you can view the logs.


## 7. Cloud Storage
Cloud Storage allows world-wide storage and retrieval of any amount of data at any time. You can use Cloud Storage for a range of scenarios including serving website content, storing data for archival and disaster recovery, or distributing large data objects to users via direct download. It's used for Unstructured Data. Organized in buckets.

## 7.1 Create a bucket
<details>
  <summary>Bucket naming rules</summary>

  - Do not include sensitive information in the bucket name, because the bucket namespace is global and publicly visible.
  - Bucket names must contain only lowercase letters, numbers, dashes (-), underscores (_), and dots (.). **Names containing dots require verification.**
  - Bucket names must start and end with a number or letter.
  - Bucket names must contain 3 to 63 characters. Names containing dots can contain up to 222 characters, but each dot-separated component can be no longer than 63 characters.
  - Bucket names cannot be represented as an IP address in dotted-decimal notation (for example, 192.168.5.4).
  - Bucket names cannot begin with the "goog" prefix.
  - Bucket names cannot contain "google" or close misspellings of "google".
  - For DNS compliance and future compatibility, you should not use underscores (_) or have a period adjacent to another period or dash. For example, ".." or "-." or ".-" are not valid in DNS names.
  
</details>

### To make a bucket
> `mb` **make bucket** commnad
>
> Remember use the Bucket naming rules

```shell
gsutil mb gs://<YOUR-BUCKET-NAME>
```
> This command is creating a bucket with default settings.
>
> To see default settings, use the Cloud console **Navigation menu** > **Cloud Storage**, then click on your bucket name, and click on the Configuration tab.

> [!Note]
> If the bucket name is already taken, either by you or someone else, try again with a different bucket name.

## 7.2 Upload an object into your bucket

### To download this image (ada.jpg) into your bucket
```shell
curl https://upload.wikimedia.org/wikipedia/commons/thumb/a/a4/Ada_Lovelace_portrait.jpg/800px-Ada_Lovelace_portrait.jpg --output ada.jpg
```
### To upload the image from the location where you saved it to the bucket you created
```shell
gsutil cp ada.jpg gs://YOUR-BUCKET-NAME
```

> [!Note]
> When typing your bucket name, you can use the tab key to autocomplete it.
>
> You can see the image load into your bucket from the command line.

### Now remove the downloaded image
```shell
rm ada.jpg
```

## 7.3 Download an object from your bucket

### To download the image you stored in your bucket to Cloud Shell
```shell
gsutil cp -r gs://YOUR-BUCKET-NAME/ada.jpg .
```

<details>
  <summary>Expected Output</summary>

  ```shell
  Copying gs://YOUR-BUCKET-NAME/ada.jpg...
  / [1 files][360.1 KiB/2360.1 KiB]
  Operation completed over 1 objects/360.1 KiB.
  ```
</details>


## 7.4. Copy an object to a folder in the bucket
### To create a folder called image-folder and copy the image (ada.jpg) into it
```shell
gsutil cp gs://YOUR-BUCKET-NAME/ada.jpg gs://YOUR-BUCKET-NAME/image-folder/
```

> [!Note[
> Compared to local file systems, folders in Cloud Storage have limitations, but many of the same operations are supported.

<details>
  <summary>Expected Output</summary>

  ```shell
  Copying gs://YOUR-BUCKET-NAME/ada.jpg [Content-Type=image/png]...
  - [1 files] [ 360.1 KiB/ 360.1 KiB]
  Operation completed over 1 objects/360.1 KiB
  ```
</details>

## 7.5 List contents of a bucket or folder
### To list the contents of the bucket
```shell
gsutil ls gs://YOUR-BUCKET-NAME
```

<details>
  <summary>Expected Output</summary>

  ```shell
  gs://YOUR-BUCKET-NAME/ada.jpg
  gs://YOUR-BUCKET-NAME/image-folder/
  ```
</details>

## 7.6 List details for an object
### To get some details about the image file you uploaded to your bucket
```shell
gsutil ls -l gs://YOUR-BUCKET-NAME/ada.jpg
```

<details>
  <summary>Expected Output</summary>

  ```shell
  306768  2017-12-26T16:07:570Z  gs://YOUR-BUCKET-NAME/ada.jpg
  TOTAL: 1 objects, 30678 bytes (360.1 KiB)
  ```
</details>

## 7.7 Make your object publicly accessible

> `acl` Acces Control list. Mechanism you can use to define who has acces to your bucket and objects.

### To grant all users read permission for the object stored in your bucket
```shell
gsutil acl ch -u AllUsers:R gs://YOUR-BUCKET-NAME/ada.jpg
```

<details>
  <summary>Expected Output</summary>

  ```shell
  Updated ACL on gs://YOUR-BUCKET-NAME/ada.jpg
  ```
</details>

> Your image is now public, and can be made available to anyone.

### Validate that your image is publicly available

Go to **Navigation menu** > **Cloud Storage**, then click on the name of your bucket.

You should see your image with the Public link box. Click the Copy URL and open the URL in a new browser tab.

## 7.8 Remove public access
### To remove this permission, use the command:
```shell
gsutil acl ch -d AllUsers gs://YOUR-BUCKET-NAME/ada.jpg
```

<details>
  <summary>Expected Output</summary>

  ```shell
  Updated ACL on gs://YOUR-BUCKET-NAME/ada.jpg
  ```
</details>

> Verify that you've removed public access by clicking the Refresh button in the console. The checkmark will be removed.

### To delete an object - the image file in your bucket
```shell
gsutil rm gs://YOUR-BUCKET-NAME/ada.jpg
```

<details>
  <summary>Expected Output</summary>

  ```shell
  Removing gs://YOUR-BUCKET-NAME/ada.jpg...
  ```
</details>

> Refresh the console. The copy of the image file is no longer stored on Cloud Storage (though the copy you made in the image-folder/ folder still exists). 
