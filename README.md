# Google Cloud Computing Foundations
Repository of my study from [Gooogle Cloud Computing Foundations Certificate](https://www.cloudskillsboost.google/paths/36) & [Google Cloud Essentials](https://www.cloudskillsboost.google/course_templates/621).

# Table of contents
- [Cloud Shell](#cloud-shell)
  - [Regions and Zones](#regions-and-zones)
- [1. Virtual Machine](#1-create-a-virtual-machine)
  - [1.1 Windows Server](#11-remote-desktop-rdp-into-the-windows-server)
  - [1.2 SSH Conection](#12-use-ssh-to-connect-to-your-instance) 
- [2. NGINX Web Server](#2-create-a-nginx-web-server)
  - [2.1 Update the Firewall](#21-update-the-firewall)


## Cloud Shell `>_`
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
### Create a new VM instance
```shell
gcloud compute instances create <INSTANCE_NAME> --machine-type <MACHINE_NAME> --zone=$ZONE
```

<details>
<summary>Expected Output</summary> 
  
```shell
Created [..."INSTANCE_NAME"].
     NAME: "INSTANCE_NAME"
     ZONE:  "ZONE"
     MACHINE_TYPE: "MACHINE_NAME"
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


