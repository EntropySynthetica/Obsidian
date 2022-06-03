# Terraform

Terraform is an open-source Infrastructure as a Code tool.

Instead of writing the code to create the infrastructure, you define a plan of what you want to be executed, and you let Terraform create the resources on your behalf. The plan isn't written in YAML, though. Instead Terraform uses a language called HCL - HashiCorp Configuration Language. In other words, you use HCL to declare the infrastructure you want to be deployed, and Terraform executes the instructions. Terraform uses plugins called providers to interface with the resources in the cloud provider.

This further expands with modules as a group of resources and are the building blocks you will use to create a cluster.

## Terraform Project Structure

A [resource](https://www.terraform.io/docs/configuration/resources.html) is an entity of a cloud service declared in Terraform code that is created according to specified and inferred properties. Multiple resources form infrastructure with their mutual connections.

Terraform uses a specialized programming language for defining infrastructure, called Hashicorp Configuration Language (HCL). HCL code is typically stored in files ending with the extension tf. A Terraform project is any directory that contains tf files and which has been initialized using the init command, which sets up Terraform caches and default local state.

Terraform state is the mechanism via which it keeps track of resources that are actually deployed in the cloud. State is stored in backends (locally on disk or remotely on a file storage cloud service or specialized state management software) for optimal redundancy and reliability. You can read more about different backends in the [Terraform documentation](https://www.terraform.io/docs/backends/index.html).

[Modules](https://www.terraform.io/docs/configuration/modules.html) in Terraform (akin to libraries in other programming languages) are parametrized code containers enclosing multiple resource declarations. They allow you to abstract away a common part of your infrastructure and reuse it later with different inputs

## File Structure

Simple Structure
A simple structure is suitable for small and testing projects, with a few resources of varying types and variables. It has a few configuration files, usually one per resource type (or more helper ones together with a main), and no custom modules, because most of the resources are unique and there aren’t enough to be generalized and reused. Following this, most of the code is stored in the same directory, next to each other. These projects often have a few variables (such as an API key for accessing the cloud) and may use dynamic data inputs and other Terraform and HCL features, though not prominently.


- main.tf – The main Azure resources groups to deploy
- env_prod.tf – Production resources to deploy
- env_stage.tf – Stage resources to deploy
- env_sandbox.tf – Sandbox/Dev resources to deploy
- variables.tf – Containing variables that will shared in other .tf files
- azure.tfvars – Defines values for variables.  This file may contain sensitve info and is not stored in version control.  
- terraform.tfstate & terraform.tfstate.backup, – These are related to the current terraform state, this is what Terraform compares against when you run plan, apply or destroy.

## Initialize Terraform State
Used to Used to initialise the Terraform directory and set the inital state.

```
terraform init
```

## Import Existing Resource

Terraform can import existing Azure infrastructure to bring it under Terraform management. Using import, you can import resources into the state. Unfortunately, at this time, Terraform cannot generate a configuration. Because of this, you have to write the resource configuration block manually for the added resource.

1. Modify the Terraform configuration and variable files as need with the new resource.

2. Run the terraform plan command again to get the address information for the resource to import. You need the address information in order to add it to the Terraform state file using the import command. You will not actually apply this plan like you did when setting up the environment.

3. Next, you need the resource ID of the Resource in Azure. 

4. Use the terraform import command and specify the Terraform resource address and resource ID.

    - terraform_id: This is the Terraform internal resource id I assigned in the configuration file. The format is <RESOURCETYPE>.<ID>.

    - azure_resource_id: This is the resource id of the resource in the Azure platform using. The format is /SUBSCRIPTIONS/SUBSCRIPTIONID/RESOURCEGROUPS/RESOURCEGROUPID/PROVIDERS/PROVIDERNAME/… You can get that value from the the Azure portal (navigate to the resource, normally this value is visible in the properties section)

```
terraform import <terraform_id> <Azure Resource ID>
```

Example
```
terraform import azurerm_key_vault.myvault /subscriptions/000000-000-0000-0000-0000000000/resourceGroups/ProdRG/providers/Microsoft.KeyVault/vaults/myvault
```

5. Terraform will output the results of the import command. You can also open the terraform.tfstate file and verify the resource is now listed (but don’t modify the state file manually!).

6. If you run the terraform plan command again, Terraform should show that nothing needs to be changed in the environment. This means Terraform did not detect any differences between your configuration and the existing resources.



## Plan
This will compare your Terraform config to what is provisioned and provide you a list of changes it would like to make.  No changes will happen in this step. 

```
terraform plan -var-file="azure.tfvars"
```

## Apply  
This will deploy your Terraform config.  Before applying it will provide a synopsis of what changes need to be made to make the config reality.  Terraform will prompt you if you want to do this before continuing.  

```
terraform apply -var-file="azure.tfvars"
```

## Show State
You can see Terraforms current state of everything deployed by Terraform with the following command. 

```
terraform show
```

## Outputs

After terraform has ran outputs can be defined which supply data about the resources that have been provisioned.  These outputs can be called by other resources, or can be output for the user.  

Sensitive outputs will be masked can be viewed by running

```
terraform output <output_name>
```

Example: 
```
terraform output kube_config
```

## Destroy

The terraform destroy command is a convenient way to destroy all remote objects managed by a particular Terraform configuration.

While you will typically not want to destroy long-lived objects in a production environment, Terraform is sometimes used to manage ephemeral infrastructure for development purposes, in which case you can use terraform destroy to conveniently clean up all of those temporary objects once you are finished with your work.


This will run terraform plan in destroy mode, showing you the proposed destroy changes without executing them.

```
terraform plan -destroy
```

The terraform destroy command terminates resources managed by your Terraform project. This command is the inverse of terraform apply in that it terminates all the resources specified in your Terraform state. It does not destroy resources running elsewhere that are not managed by the current Terraform project.
```
terraform destroy
```

To destroy specific resources a target can be specified
```
terraform destroy --target <terraform_id>
```

Example:
```
terraform destroy -var-file="azure.tfvars" --target azurerm_kubernetes_cluster.sandboxAKS
```