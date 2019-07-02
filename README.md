__TL;DR: Recommended Usage:__
1. create-tfm-backend.sh
2. create-aad-server-app.sh
3. 
# Terraform-Backend-OS
Non-terraform scripts to create backend for Open Source Terraform running in a container in Azure.
Backend consists of an Azure storage account, key vault, secrets, container, and Azure AD integration.

# Create a Storage Account to manage terraform state for different clusters
Execute **create-tfm-backend.sh** while specifying:
### Location
* ex: eastus
### Resource group
* The resource group will be created with the name specified in the region specified
* ex: testuseatfmrg
* tfm = terraform
### Storage Account
* This will be where the tfstate and terraform config files will be stored
* This and every subsequent azure object will be created in the same resource group specified.
* ex: testuseatfmstac
### Container Name
* Container that manages the storage account
* ex: testuseatfmtfstate
### Key Vault Name
* Key Vault that will sotre all terraform related secrets
* A secret named **"terraform-backend-key"*** will also be created
  * This will allow terraform to access the storage account
* ex: testuseatfmkv
## Example: 
source **create-tfm-backend.sh** eastus testuseatfmrg testuseatfmstac testuseatfmtfstate testuseatfmkv

### Initialise terraform for AKS deployment
* This step will intialize Terraform with the storage account as backend to store the tfstate file in the container created in the first step above
* The tfstate will get its name from the resource group - the "rg"
* ex: if "testuseatfmrg", then "testuseatfm"

# Create a custom terraform service principal with least privilege to perform the AKS deployment
* *This script (create-tfm-sp.sh) will be kicked off automatically following the completion of create-tfm-backend.sh*
* The script will interactively:
 * Create the service principal (or resets the credentials if it already exists)
 * Prompts to choose either a populated or empty provider.tf azurerm provider block
 * Exports the environment variables if you selected an empty block (and display the commands)
 * Display the az login command to log in as the service principal
 * Export the TF_VAR_client_id and TF_VAR_client_secret to a local file (export_tf_vars.txt)
  * And to the Azure Key Vault vreated previously
 
 # Azure Active Directory Authorization
* In order to enable Azure Active Directory authorization with Kubernetes, you need to create two applications:
 * A server application, that will work with Azure Active Directory
 * A client application, that will work with the server application
* Multiple AKS clusters can use the same server application, but it’s recommended to have one client application per cluster.
