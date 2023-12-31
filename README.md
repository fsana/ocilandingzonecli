# Configure OCI Secure Landing Zone Using Terraform CLI

## Lab 1: Clone the Landing Zone GitHub Repository Locally or in Cloud Shell

### 1.1 Configure API Keys

Generate API Keys:

![Generate API Keys](images/screenshot1.png "Generate API Keys")

Get configuration information:

[DEFAULT]
user=ocid1.user.oc1..aaaaaaaa5k3w4svyy4dsn6uxcnhgiec62g337aytu5s4vnsztjkfth2bfb4q
fingerprint=cd:72:9a:68:f8:5e:90:dd:7e:f8:72:26:a9:3a:5d:22
tenancy=ocid1.tenancy.oc1..aaaaaaaawrcp476mcq6qeblp6pzemr6jwz7zvuylhnh3ufjkrzrow7iu7g3q
region=us-phoenix-1
key_file=<path to your private keyfile> # TODO


It is useful to add this to your ~/.oci/config file to be further used with OCI CLI.

### 1.2 Clone Landing Zone Terraform GitHub Repository

Location of the repository:

```
https://github.com/oracle-quickstart/oci-cis-landingzone-quickstart
```

### 1.3 Clone the repository

```
$ git clone https://github.com/oracle-quickstart/oci-cis-landingzone-quickstart
Cloning into 'oci-cis-landingzone-quickstart'...
remote: Enumerating objects: 9355, done.
remote: Counting objects: 100% (4112/4112), done.
remote: Compressing objects: 100% (930/930), done.
remote: Total 9355 (delta 3102), reused 3923 (delta 3020), pack-reused 5243
Receiving objects: 100% (9355/9355), 16.17 MiB | 8.04 MiB/s, done.
Resolving deltas: 100% (6320/6320), done.
```


### 1.4 Modify the Configuration of the Landing Zone

Open the repository directory in your favorite text editor. Best solution Visual Studio Code with Terraform Plugin Enabled.

The file that must be edited to deploy the landing zone/minimal configuration is ./config/quickstart-input.tfvars

Values are needed for: 

tenancy_ocid         = "<tenancy_ocid>"
user_ocid            = "<user_ocid>"
fingerprint          = "<user_api_key_fingerprint>"
private_key_path     = "<path_to_user_private_key_file>"
private_key_password = ""
region               = "<tenancy_region>"
service_label        = "<a_label_to_prefix_resource_names_with>"
cis_level            = "1"

Email addresses for:

network_admin_email_endpoints    = ["<email1>","<email2>","...","<emailn>"] 
security_admin_email_endpoints   = ["<email1>","<e-mail2>","...","<emailn>"]


All the other values are not mandatory and they have default values. Of course those can be changed by the user.


Example of a configuration using API key config:

```
tenancy_ocid         = "ocid1.tenancy.oc1..aaaaaaaawrcp476mcq6qeblp6pzemr6jwz7zvuylhnh3ufjkrzrow7iu7g3q"
user_ocid            = "ocid1.user.oc1..aaaaaaaa5k3w4svyy4dsn6uxcnhgiec62g337aytu5s4vnsztjkfth2bfb4q"
fingerprint          = "cd:72:9a:68:f8:5e:90:dd:7e:f8:72:26:a9:3a:5d:22"
private_key_path     = "/Users/flaviusssana/oci/workshop_bucharest/keys/flavius.sana-06-23-08-54.pem"
private_key_password = ""
region               = "us-phoenix-1"
service_label        = "demo1"
cis_level            = "1"


network_admin_email_endpoints    = ["flavius.sana@oracle.com"] 
security_admin_email_endpoints   = ["flavius.sana@oracle.com"]
```

## Lab 2. Execute Terraform Commands


### 2.1 Initialize the Repository

In the cloned repository config directory execute:

Initialize the repository. This command with initialize the provider (OCI Terraform Provider and required modules)

```
$ terraform init

Initializing the backend...
Initializing modules...
- lz_app_bastion in ../modules/security/bastion
- lz_arch_center_tag in ../modules/monitoring/tags-arch-center
- lz_buckets in ../modules/object-storage/bucket
- lz_cloud_guard in ../modules/monitoring/cloud-guard
...
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
``` 

Look for the message: "Terraform has been successfully initialized!"

### 2.2 Generate Execution Plan 

Generate an execution plan. An execution plan, generated by Terraform plan command creates a graph of the resources that are going to be created, modified or deleted. For saving the execution plan execute "terraform plan" command with -out option to save the execution plan in the file specified in the "out" parameter. 

Another option required is the variable file to be used. The default "terraform.tfvars" is not present in our root directory (config) and we must specify one using -var-file option.

```
$ terraform plan -out plan1.out -var-file quickstart-input.tfvars
...
 + vcns                        = {
      + demo1-0-vcn = {
          + cidr_block = "10.0.0.0/20"
          + dns_label  = "demo10vcnphx"
          + id         = (known after apply)
        }
    }

────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Saved the plan to: plan1.out

To perform exactly these actions, run the following command to apply:
    terraform apply "plan1.out"

```

### 2.3 Create the Landing Zone Resources

To create the landing zone resources execute "terraform apply" command using the plan file generated previously

```
$ terraform apply plan1.out
module.lz_groups.oci_identity_group.these["demo1-database-admin-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-announcement-reader-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-auditor-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-storage-admin-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-security-admin-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-iam-admin-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-cred-admin-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-network-admin-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-appdev-admin-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-cost-admin-group"]: Creating...
module.lz_groups.oci_identity_group.these["demo1-storage-admin-group"]: Creation complete after 3s [id=ocid1.group.oc1..aaaaaaaaddoikrotevnotm5cxirnqv6smmnrdh5irdipgr4fcdmb7tvehtkq]
module.lz_arch_center_tag[0].oci_identity_tag_namespace.arch_center: Creating...
module.lz_groups.oci_identity_group.these["demo1-appdev-admin-group"]: Creation complete after 3s [id=ocid1.group.oc1..aaaaaaaakarhxadnrlufp5x76ugrelslcu5xgwatp7hj5ajtgksa6cctd5ca]
....

```

Once the apply operation was completed successfully you can start using the landing zone.

## Lab 3. Tear-up

### 3.1 Remove Landing Zone Resources

Using ```terraform destroy``` command, resources created by the landing zone scripts can be removed. Execute

```
$ terraform destroy -var-file quickstart-input.tfvars -auto-approve
...
module.lz_compartments.oci_identity_compartment.these["demo1-security-cmp"]: Destruction complete after 0s
module.lz_compartments.oci_identity_compartment.these["demo1-database-cmp"]: Destruction complete after 0s

Destroy complete! Resources: 101 destroyed.
```

Note that the compartments are not deleted automatically. Manual cleanup is required for the compartments. 

![Manually delete Compartments](images/screenshot2.png "Delete Compartments")