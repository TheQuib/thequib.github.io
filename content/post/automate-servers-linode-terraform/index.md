---
author: "Quinn Henry"
title: "Automate server deployment in Linode"
date: "2022-05-16"
description: "Automate the deployment of servers in minutes using Terraform, feat. Minecraft"
tags: ["terraform", "linode", "devOps", "cloud", "automation"]
categories: ["Terraform", "Linode", "Automation"]
image: featuredImage.png
---

In case you're not aware, Terraform is pretty cool. It is super powerful, providing the ability to provision infrastructure in seconds using code. Linode and it's Terraform provider are no exception to this ability. Let's dive into that...

&nbsp;

## Requirements

 - A machine with Terraform installed
 - A Linode account
   - With an API token, [here's how you can get this](https://www.linode.com/docs/products/tools/linode-api/guides/get-access-token/)
     - This will need at least `Read/Write` access to `Linodes`
   - With the ability to create nodes (such as with free credits, or with an added bank account)


If you do need to add your bank account, don't worry! Because of the awesomeness of Terraform, *this will only cost a few cents* as long as you [destroy the infrastructure](#destroy-infrastructure) when it's all said and done.


------

## GitHub Repo

All configuration files and example files can be found on my [Overture](https://github.com/TheQuib/overture) repo, under [Terraform/Compute/Linode/SimpleLinode](https://github.com/TheQuib/overture/tree/master/Terraform/Compute/Linode/SimpleLinode).

## Create some Terraform files

First thing's first, let's create the Terraform files that will define this project.
 - ***Note:*** *You'll want to make sure you're in an empty directory*

&nbsp;

### `provider.tf` 
 - Defines the necessary provider to use: [linode/linode](https://registry.terraform.io/providers/linode/linode/latest)

```tf
terraform {
    required_version = ">= 0.13"
    required_providers {
        linode = {
            source  = "linode/linode"
            version = "1.27.2"
        }
    }
}

# Configure the Linode Provider
provider "linode" {
    token = var.linode_token
}
```

&nbsp;

### `variables.tf`
 - Defines the variables Terraform will use

```tf
variable linode_token {
    type = string
    description = "API token created in linode with access to desired resources"
    sensitive = true
}

# Instance Settings
variable linode_instance_label {
    type = string
    description = "Label for the Linode instance"
}
variable linode_instance_image {
    type = string
    description = "Image for the Linode instance"
}
variable "linode_instance_region" {
    type = string
    description = "Region to place the Linode instance"
}
variable "linode_instance_type" {
    type = string
    description = "Type of Linode instance to create"
}
variable "linode_instance_ssh_keys" {
    type = list
    description = "List of authorized SSH keys to install on the instance"
}
variable "linode_instance_root_pass" {
    type = string
    description = "Root password for Linode instance"
    sensitive = true
}
```

&nbsp;

### `credentials.auto.tfvars` 
 - Places values on the variables
   - You will need to fill in these values as you see fit, make sure to provide **your** [Linode token](https://www.linode.com/docs/products/tools/linode-api/guides/get-access-token/)
   - ***Note:*** *This should not be a tracked file for anyone to see, it contains sensitive information*

```tf
linode_token = "yourLinodeToken"

# Instance settings
linode_instance_label = "terraformInstance"
linode_instance_image = "linode/ubuntu22.04" # https://api.linode.com/v4/images
linode_instance_region = "us-east" # https://api.linode.com/v4/regions
linode_instance_type = "g6-nanode-1" # https://api.linode.com/v4/linode/types
linode_instance_ssh_keys = [
    "ssh-rsa Key1AAAA...Gw== user@example.local",
    "ssh-rsa Key2BBBB...Gw== user2@example.local"
]
linode_instance_root_pass = "yourRootPassHere"

# Minecraft settings
minecraft_server_url = "https://launcher.mojang.com/v1/objects/c8f83c5655308435b3dcf03c06d9fe8740a77469/server.jar" # From https://www.minecraft.net/en-us/download/server
```

## Define the linode_instance

Now, the big (yet small) file that will do the cool stuff:

&nbsp;

### `createSimpleInstance.tf`

```tf
resource "linode_instance" "web" {
    label = var.linode_instance_label
    image = var.linode_instance_image
    region = var.linode_instance_region
    type = var.linode_instance_type
    #authorized_keys = ["ssh-rsa AAAA...Gw== user@example.local"]
    root_pass = var.linode_instance_root_pass

    #group = "foo"
    #tags = [ "foo" ]
    #swap_size = 256
    #private_ip = true
}
```

*Note  that there are a few commented lines in case you would like to use them.*

This file will take the values of the variables set in [credentials.auto.tfvars](#credentialsautotfvars) and create a linode instance based on those values.

## Apply configuration

Now, you're ready to apply!

Go ahead and run

```bash
terraform plan
```

To see what Terraform will do

And run

```bash
terraform apply -auto-approve
```

To actually make the changes in Linode


## Check your work

Now, log into the Linode console, and watch the magic happen! You should see a new Linode create with the settings you have in the [credentials.auto.tfvars](#credentialsautotfvars) file.


## Destroy Infrastructure

If you're like me, you may be paying for this to be up and running. And unless you want to use this server you just created for something, you'll want to destroy it so no extra charges come into your account.

To destroy your infrastructure with Terraform, run

```bash
terraform destroy -auto-approve
```
 
And you can watch it magically disappear!

&nbsp;