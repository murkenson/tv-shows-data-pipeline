# Reproduce the project

## Prerequisites

- A [Google Cloud Platform](https://cloud.google.com/) account.
- A [Kaggle](https://kaggle.com/) account.

> [!NOTE]
GCP is used for its 90-day trial offer of $300 and its comprehensive platform, as mentioned previously.


## STEP 1: Create a Google Cloud Project

Access the [Google Cloud dashboard](https://console.cloud.google.com/) and create a new project `de-final-project` from the dropdown menu on the top left of the screen, to the right of the _Google Cloud Platform_ text.

After you create the project, you will need to create a [_Service Account_](https://console.cloud.google.com/iam-admin) with the following roles:

* `BigQuery Admin`
* `Storage Admin`
* `Storage Object Admin`
* `Viewer`

Download the Service Account credentials file. 

> [!IMPORTANT]
All steps will be performed using a VM as recommended. On the next step, we will upload this credentials file to the VM.

You will also need to activate the following APIs:
* https://console.cloud.google.com/apis/library/iam.googleapis.com
* https://console.cloud.google.com/apis/library/iamcredentials.googleapis.com
* https://console.cloud.google.com/marketplace/product/google/compute.googleapis.com


<details>
<summary>You should see this.</summary>

![Goocle Cloud](/recreate_project/static/001_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/002_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/003_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/004_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/005_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/006_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/007_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/008_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/009_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/010_google_cloud_setup.png)

</details>
</br>

## STEP 2: Create SSH key pairs

### Linux and macOS

To execute on Linux and macOS, enter the subsequent command. Ensure to substitute `USERNAME` with your computer's username. Additionally, replace `KEY_FILENAME` with a more conventional filename such as `gcs_key_3252`.

```bash
ssh-keygen -t rsa -f ~/.ssh/KEY_FILENAME -C USERNAME -b 2048
```

`ssh-keygen` saves your private key file to `~/.ssh/KEY_FILENAME` and your public key file to `~/.ssh/KEY_FILENAME.pub`.


### Add SSH keys to VM

1) Begin by copying and uploading the public key `KEY_FILENAME.pub` to GCP.
2) Navigate to the Google Cloud console, choose **Compute Engine** from the left menu
3) Then access **Metadata** and proceed to the **SSH KEYS** tab. 
4) Click on the **ADD SSH KEY** button.
3) Paste the public key into the designated field and save your changes by clicking the **SAVE** button.
   
>To simplify copying the contents of your public `KEY_FILENAME.pub` file, use the following command:

```bash
cat ~/.ssh/KEY_FILENAME.pub | pbcopy
```


>To access your virtual machine, just run this command:

```bash
ssh -i ~/.ssh/KEY_FILENAME USERNAME@EXTERNAL_IP
```

Respond to the confirmation prompt with a `yes`.



<details>
<summary>You should see this.</summary>

![Goocle Cloud](/recreate_project/static/014_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/015_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/016_google_cloud_setup.png)

</details>
</br>


## STEP 3: Create API TOKEN on Kaggle

[Go to this link](https://www.kaggle.com/settings/account).
If you already have an account, click on `CREATE NEW TOKEN`. If you don't have an account, sign up first.


<details>
<summary>You should see this.</summary>

![Kaggle Account](/recreate_project/static/020_kaggle_setup.png)

</details>
</br>

> [!NOTE]
This token should be added to the `.env` file in the next step.

## STEP 4: Create a VM instance

### Using the GCP dashboard

1. From your project's dashboard, go to _Cloud Compute_ > _VM instance_
1. Create a new instance:
    * Choose name `final-project`
    * Pick region `us-central1-c`. You can check out the regions [in this link](https://cloud.google.com/about/locations).
    * Pick a _E2 series_ instance. A _e2-standard-4_ instance is recommended (2 vCPUs, 4GB RAM)
    * Change the boot disk to _Ubuntu_. The _Ubuntu 20.04 LTS_ version is recommended. Also pick at least 20GB of storage.
    * Leave all other settings on their default value and click on _Create_.


> [!IMPORTANT]
Make sure that you use the same region for all of your Google Cloud components

<details>
<summary>You should see this.</summary>

![Goocle Cloud](/recreate_project/static/011_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/012_google_cloud_setup.png)

![Goocle Cloud](/recreate_project/static/013_google_cloud_setup.png)



</details>
</br>

## STEP 5: Install packages on the Virtual Machine


## Instal make 

>To install the `make` in the Ubuntu Terminal, use the following command:
```bash
sudo apt update && sudo apt install make -y
```

### Download the github project

>Downloading project from github
```bash
git clone https://github.com/murkenson/movies_tv_shows_data_pipeline final_project && cd final_project
```


### Set an environment variable for the credentials

Let the [environment variable point to your GCP key](https://cloud.google.com/docs/authentication/application-default-credentials#GAC), authenticate it and refresh the session token

>Paste your the Service Account credentials
```bash
make setup
```

>Activate the environment variable.
```bash
make config
```

### Isntall Docker, Docker compose & Terraform

Assuming you are using Linux AMD64 run the following commands to install Terraform - if you are using a different OS please choose the correct version here and exchange the download [link](https://developer.hashicorp.com/terraform/install#linux) and zip file name

>RUN commnad to install packages
```bash
make install_pack
```


>Activate the environment variable
```bash
source ~/.bashrc
```

> [!IMPORTANT]
Log out and then log back in to have your group membership re-evaluated.


### Creating a Google Cloud Storage (GCS) bucket and a BigQuery dataset.

>To initiate, plan and apply the necessary infrastructure, please execute the following Terraform commands, adjusting as needed. Please provide the ID of your project.
```bash
cd ~/final_project/terraform/  && terraform init
```

```bash
terraform plan  --var="project=GCLOUD_PROJECT_NAME"
```

```bash
terraform apply --var="project=GCLOUD_PROJECT_NAME"  -auto-approve
```




<details>
<summary>You should see this.</summary>

>init command
```
Initializing the backend...

Initializing provider plugins...
- Reusing previous version of hashicorp/google from the dependency lock file
- Using previously-installed hashicorp/google v5.6.0

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```
>Plan command
```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # google_bigquery_dataset.ddataset will be created
  + resource "google_bigquery_dataset" "ddataset" {
      + creation_time              = (known after apply)
      + dataset_id                 = "movies_tv_shows_8c93fb215204"
      + default_collation          = (known after apply)
      + delete_contents_on_destroy = false
      + effective_labels           = (known after apply)
      + etag                       = (known after apply)
      + id                         = (known after apply)
      + is_case_insensitive        = (known after apply)
      + last_modified_time         = (known after apply)
      + location                   = "US"
      + max_time_travel_hours      = (known after apply)
      + project                    = "de-final-project-416207"
      + self_link                  = (known after apply)
      + storage_billing_model      = (known after apply)
      + terraform_labels           = (known after apply)
    }

  # google_storage_bucket.data-lake-bucket will be created
  + resource "google_storage_bucket" "data-lake-bucket" {
      + effective_labels            = (known after apply)
      + force_destroy               = true
      + id                          = (known after apply)
      + location                    = "US"
      + name                        = "movies_tv_shows_2125bf6f3c04"
      + project                     = (known after apply)
      + public_access_prevention    = (known after apply)
      + self_link                   = (known after apply)
      + storage_class               = "STANDARD"
      + terraform_labels            = (known after apply)
      + uniform_bucket_level_access = (known after apply)
      + url                         = (known after apply)

      + lifecycle_rule {
          + action {
              + type = "AbortIncompleteMultipartUpload"
            }
          + condition {
              + age                   = 1
              + matches_prefix        = []
              + matches_storage_class = []
              + matches_suffix        = []
              + with_state            = (known after apply)
            }
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

>Apply command

```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # google_bigquery_dataset.ddataset will be created
  + resource "google_bigquery_dataset" "ddataset" {
      + creation_time              = (known after apply)
      + dataset_id                 = "movies_tv_shows_8c93fb215204"
      + default_collation          = (known after apply)
      + delete_contents_on_destroy = false
      + effective_labels           = (known after apply)
      + etag                       = (known after apply)
      + id                         = (known after apply)
      + is_case_insensitive        = (known after apply)
      + last_modified_time         = (known after apply)
      + location                   = "US"
      + max_time_travel_hours      = (known after apply)
      + project                    = "de-final-project-416207"
      + self_link                  = (known after apply)
      + storage_billing_model      = (known after apply)
      + terraform_labels           = (known after apply)
    }

  # google_storage_bucket.data-lake-bucket will be created
  + resource "google_storage_bucket" "data-lake-bucket" {
      + effective_labels            = (known after apply)
      + force_destroy               = true
      + id                          = (known after apply)
      + location                    = "US"
      + name                        = "movies_tv_shows_2125bf6f3c04"
      + project                     = (known after apply)
      + public_access_prevention    = (known after apply)
      + self_link                   = (known after apply)
      + storage_class               = "STANDARD"
      + terraform_labels            = (known after apply)
      + uniform_bucket_level_access = (known after apply)
      + url                         = (known after apply)

      + lifecycle_rule {
          + action {
              + type = "AbortIncompleteMultipartUpload"
            }
          + condition {
              + age                   = 1
              + matches_prefix        = []
              + matches_storage_class = []
              + matches_suffix        = []
              + with_state            = (known after apply)
            }
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

google_bigquery_dataset.ddataset: Creating...
google_storage_bucket.data-lake-bucket: Creating...
google_bigquery_dataset.ddataset: Creation complete after 1s [id=projects/de-final-project-416207/datasets/movies_tv_shows_8c93fb215204]
google_storage_bucket.data-lake-bucket: Creation complete after 1s [id=movies_tv_shows_2125bf6f3c04]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```


</details>
</br>



### Installing Mage AI


> RUN command to open `.env` file
```bash
cd ~/final_project/ && make edit_env
```

>Add information about your Kaggle API token and gcloud project ID
```
KAGGLE_USERNAME=<FILL_KAGGLE_USERNAME>
KAGGLE_KEY=<FILL_KAGGLE_KEY>
GCLOUD_PROJECT_NAME=<FILL_GCLOUD_PROJECT_NAME>
```


>Starts the Docker containers in detached mode after copying the contents of dev.env to .env and building the Docker images based on the docker-compose.yml.
```bash
cd ~/final_project && make build_run_docker
```



### Running pipeline


>RUN the pipeline from web: 

1. Access the Mage AI web interface at http://localhost:6789/. 
2. Click on the `pipeline_for_movies_tv_shows_datasets_from_kaggle` pipeline. 
3. Click on `Run Pipeline Now` and `Run Now`. 
4. Enter the trigger and check the pipeline's results.


>You should see this

![](/recreate_project/static/033_mage_pipeline_start.gif)

![](/recreate_project/static/032_mage_pipeline.gif)


### Checking uploaded tables

Go to BigQuery, open the `movies_tv_shows_8c93fb215204` dataset.

<details>
<summary>You should see this.</summary>

![BigQuery](/recreate_project/static/070_bigquery_tables.png)

</details>
</br>


### Replicating dashboard

To replicate the dashboard, please follow these steps:

 1. Go to the dashboard's [URL](https://lookerstudio.google.com/reporting/7ef1cb87-8bd6-4b62-8946-3ea0e79a7ea6). 
 
 2. Click on the `More Options` icon in the top-right corner of the screen. 
 
 3. Select `Make a Copy` from the menu that appears.


<details>
<summary>You should see this.</summary>

![BigQuery](/recreate_project/static/062_lookerstudio_report.png)

</details>
</br>


### When you are done


>[!IMPORTANT]
This step is important because the project uses resource-intensive services that can end up costing you money if you do not shut them down. Please provide the ID of your project.

```bash
cd ~/final_project/terraform/ \
&& terraform destroy --var="project=project=GCLOUD_PROJECT_NAME" -auto-approve
```


<details>
<summary>You should see this.</summary>

```
google_storage_bucket.data-lake-bucket: Refreshing state... [id=movies_tv_shows_2125bf6f3c04]
google_bigquery_dataset.ddataset: Refreshing state... [id=projects/de-final-project-416207/datasets/movies_tv_shows_8c93fb215204]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # google_bigquery_dataset.ddataset will be destroyed
  - resource "google_bigquery_dataset" "ddataset" {
      - creation_time                   = 1709549761733 -> null
      - dataset_id                      = "movies_tv_shows_8c93fb215204" -> null
      - default_partition_expiration_ms = 0 -> null
      - default_table_expiration_ms     = 0 -> null
      - delete_contents_on_destroy      = false -> null
      - effective_labels                = {} -> null
      - etag                            = "yOR9sg06c0zixuaF+t6fsQ==" -> null
      - id                              = "projects/de-final-project-416207/datasets/movies_tv_shows_8c93fb215204" -> null
      - is_case_insensitive             = false -> null
      - labels                          = {} -> null
      - last_modified_time              = 1709549761733 -> null
      - location                        = "US" -> null
      - project                         = "de-final-project-416207" -> null
      - self_link                       = "https://bigquery.googleapis.com/bigquery/v2/projects/de-final-project-416207/datasets/movies_tv_shows_8c93fb215204" -> null
      - terraform_labels                = {} -> null

      - access {
          - role          = "OWNER" -> null
          - user_by_email = "de-final-project@de-final-project-416207.iam.gserviceaccount.com" -> null
        }
      - access {
          - role          = "OWNER" -> null
          - special_group = "projectOwners" -> null
        }
      - access {
          - role          = "READER" -> null
          - special_group = "projectReaders" -> null
        }
      - access {
          - role          = "WRITER" -> null
          - special_group = "projectWriters" -> null
        }
    }

  # google_storage_bucket.data-lake-bucket will be destroyed
  - resource "google_storage_bucket" "data-lake-bucket" {
      - default_event_based_hold    = false -> null
      - effective_labels            = {} -> null
      - enable_object_retention     = false -> null
      - force_destroy               = true -> null
      - id                          = "movies_tv_shows_2125bf6f3c04" -> null
      - labels                      = {} -> null
      - location                    = "US" -> null
      - name                        = "movies_tv_shows_2125bf6f3c04" -> null
      - project                     = "de-final-project-416207" -> null
      - public_access_prevention    = "inherited" -> null
      - requester_pays              = false -> null
      - self_link                   = "https://www.googleapis.com/storage/v1/b/movies_tv_shows_2125bf6f3c04" -> null
      - storage_class               = "STANDARD" -> null
      - terraform_labels            = {} -> null
      - uniform_bucket_level_access = false -> null
      - url                         = "gs://movies_tv_shows_2125bf6f3c04" -> null

      - lifecycle_rule {
          - action {
              - type = "AbortIncompleteMultipartUpload" -> null
            }
          - condition {
              - age                        = 1 -> null
              - days_since_custom_time     = 0 -> null
              - days_since_noncurrent_time = 0 -> null
              - matches_prefix             = [] -> null
              - matches_storage_class      = [] -> null
              - matches_suffix             = [] -> null
              - num_newer_versions         = 0 -> null
              - with_state                 = "ANY" -> null
            }
        }
    }

Plan: 0 to add, 0 to change, 2 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

google_bigquery_dataset.ddataset: Destroying... [id=projects/de-final-project-416207/datasets/movies_tv_shows_8c93fb215204]
google_storage_bucket.data-lake-bucket: Destroying... [id=movies_tv_shows_2125bf6f3c04]
google_bigquery_dataset.ddataset: Destruction complete after 0s
google_storage_bucket.data-lake-bucket: Destruction complete after 0s

Destroy complete! Resources: 2 destroyed.
```

</details>
</br>



>For stopping container 

```bash
cd ~/final_project && make teardown
```


<details>
<summary>You should see this.</summary>

```bash
[+] Running 3/3
 ✔ Container final_project-magic-1     Removed                                                                                            10.4s
 ✔ Container movies_tv_shows-postgres  Removed                                                                                             0.3s
 ✔ Network final_project_default       Removed                                                                                             0.1s
Untagged: postgres:14
Untagged: postgres@sha256:6779d7a308f3c9c518a644ad9326be6149dc50352c91927725936a1115e09b0d
Deleted: sha256:356048a31c4059a532aee010fcfef24fdaf3415c417f713a498a8ac256a171ac
Deleted: sha256:ba442305ee10aebdfb1176c2ff302dfa77054849682e3e47c248c0a3b4758b4a
Deleted: sha256:a360dee89d220ac7a580164f17b52bd13cabc9921fc96bed8aae8c1547f7e7fd
Deleted: sha256:2e3b128bdc8e88cfcf1f21a0bd3880b9514d0b143ac47d14d98d8547b6fe9a39
Deleted: sha256:c38cb31a7904527f76964eaf24c3f846722017f85de6849fa3efb05fe6716018
Deleted: sha256:f2799c26b8912b2aa4ef9233ce92763a4e3a7496354000a2153b5229df9ef172
Deleted: sha256:87954bc86af129cd12829866ad051299d149d8a7af056c81bc742cad70796ce5
Deleted: sha256:69d375c5f90f1af9052c48d91153015c1218e818b7c1ddfc26efc8d914f199ea
Deleted: sha256:f6c00f9786742d8673e0151a3f579ab7bc2e5a51ecbd270a64295ed8f0f3d2f5
Deleted: sha256:09ddb139fb573c22b13d5a228f56786b16aaf84b5b18b7f59c47b501eb066bdc
Deleted: sha256:49a085e9ea640ba4d209fcb3ea430748da2cfc5c875da0e50c2b5a653a6004e0
Deleted: sha256:9089a35eed65c6258540ec217672b4aeddf29810d14e876025a7a9fb61ed7cb6
Deleted: sha256:de288f7cc9566b3808a6691cf52e136d98683a4b1033ed1a54da01ec9445cf48
Deleted: sha256:a74a8aa00f370c48738806be74d5195f6ef9187f97112c9b4e076ff076540fa1
Deleted: sha256:a483da8ab3e941547542718cacd3258c6c705a63e94183c837c9bc44eb608999
Untagged: mageai/mageai:latest
Deleted: sha256:dd77b7a1e3340e168466892e622e949ccc29184bf1af2365b8a30963dba197c7
```

</details>
</br>