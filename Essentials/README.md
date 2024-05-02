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
### Set the project region
```shell
gcloud config set compute/region REGION
```

### Create a variable for region
```shell
export REGION=REGION
```

### Create a variable for zone
```
export ZONE=Zone
```

> [!Note]
> When you run gcloud on your own machine, the config settings are persisted across sessions. But in Cloud Shell, you need to set this for every new session or reconnection.

### 
## 1. Creating a Virtual Machine

