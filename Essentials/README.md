# [Google Cloud Essentials](https://www.cloudskillsboost.google/course_templates/621)

## Cloud Shell
### List the active account name
```shell
gcloud auth list
```

<details>
<summary>
Output
</summary> 
  
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
<summary>
Output
</summary> 
  
```shell
[core]
project = "PROJECT_ID"
```
</details>

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
```
> To use the variable: `$VARIABLE`

> [!Note]
> When you run gcloud on your own machine, the config settings are persisted across sessions. But in Cloud Shell, you need to set this for every new session or reconnection.

## 1. Create a Virtual Machine
### Create a new VM instance
```shell
gcloud compute instances create <VM_NAME> --machine-type <MACHINE_NAME> --zone=$ZONE
```

<details>
<summary>
Output
</summary> 
  
```shell
Created [..."VM_NAME"].
     NAME: "VM_NAME"
     ZONE:  "ZONE"
     MACHINE_TYPE: "MACHINE_NAME"
     PREEMPTIBLE:
     INTERNAL_IP: 10.128.0.3
     EXTERNAL_IP: 34.136.51.150
     STATUS: RUNNING
```
</details>

### To see all defaults
```shell
gcloud compute instances create --help
```
> To exit help, press **CTRL + C**.

### Use SSH to connect to your instance
```shell
gcloud compute ssh <VM_NAME> --zone=$ZONE
```
> Disconnect from SSH by exiting from the remote shell: `exit`.
## 2. Create a NGINX Web Server

