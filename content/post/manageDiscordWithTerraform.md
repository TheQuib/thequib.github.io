---
author: "Quinn Henry"
title: "Manage Discord Servers with Terraform"
date: "2022-05-01"
description: "Managing Discord servers with code has never been easier."
tags: ["networking", "automation", "terraform", "IaC"]
ShowToc: true
ShowBreadCrumbs: true
draft: false
---

Something everyone is dying to do... Code-driven Discord server management.

&nbsp;

# Requirements

 - Discord API Token
   - [Learn how to get this](#obtain-discord-api-token)
 - Some knowledge with Terraform (to understand what is going on)
 - Terraform 0.13 and up

&nbsp;

------

&nbsp;

# Source Code

All of my code and related files can be found in my [Overture Repo](https://github.com/TheQuib/overture).

&nbsp;

------

&nbsp;

# Obtain Discord API Token

 - Head over to the [Discord Developer Portal](https://discord.com/developers/)
 - Get logged in
 - Create a new application
 - Name this whatever you want
   - Ex: "Terraform"
 - Once in your new application, head over to the "Bot" section
 - Click "Add Bot"
 - Give the bot a name (or leave the default name)
   - Ex: "Mr. Terraform"
 - Click the "Reset Token" button
   - This will give you your token, and is the only time it will be displayed
   - Make sure you keep this in a safe place, and treat it like a password
 - Use this token in your [credentials.auto.tfvars](credentials.auto.tfvars.example) file

&nbsp;

------

&nbsp;

# Terraform Provider Information

The provider used here is [discord](https://registry.terraform.io/providers/Chaotic-Logic/discord/latest) by [Chaotic Logic](https://registry.terraform.io/namespaces/Chaotic-Logic) ([Source](https://github.com/Chaotic-Logic/terraform-provider-discord)).

&nbsp;

# Get started

***Note:*** *Make sure you have your Discord API token ready*


&nbsp;


## Prepare Terraform

To get Terraform ready, create a file in the same directory called `provider.tf`, and paste the following information into it:

```tf
terraform {

    required_version = ">= 0.13.0"

    required_providers {
        discord = {
            source = "Chaotic-Logic/discord"
            version = "0.0.1"
        }
    }
}

provider "discord" {
    token = var.discord_token
}
```

Now, get the required provider downloaded (`cd` into your Terraform directory first):

```sh
terraform init
```


&nbsp;


## Create credentials file

Create a new directory, and `cd` into it. Then create a file in there called `credentials.auto.tfvars`.

Paste the following information in:

```tfvars
discord_token = "YourDiscordApiToken"

server_name = "NewServerName"

server_region = "us-east"  # Region to host in (Brazil | Europe | Hong Kong | India | Rapan | Russia | Singapore | South Africa | Sydney | US (Central | East | South | West))

category_name = "NewCategoryName"

text_channel_name = "NewTextChannelName"
```

You'll want to edit the values (in quotations) as needed, filling in `discord_token` with *your* API token received from [here](#obtain-discord-api-token), it would be best to choose a region closest to you.


&nbsp;


## Create variables file

While the `credentials.auto.tfvars` assigns values to variables, the variables still need to be defined within Terraform. Create a file called `variables.tf` to store these, paste the following contents (one per assigned variable from [above](#create-credentials-file)):

```tf
variable "discord_token" {
    type = string
    sensitive = true
}

variable "server_name" {
    type = string
}

variable "server_region" {
    type = string
}

variable "category_name" {
    type = string
}

variable "text_channel_name" {
    type = string
}
```

&nbsp;


## Create a server

In the same directory as your `provider.tf` file, create a file called `createServer.tf`.

Paste the following information:

```
# Create a server
resource "discord_server" "my_server" {
    name = var.server_name
    region = var.server_region
    default_message_notifications = 0
}



# Get newly created server's ID
data "discord_server" "createdServerInfo" {
    server_id = resource.discord_server.my_server.id
}
```

### What does this do?

 - The block `discord_server.my_server` creates a Discord server with a given name and region assigned from the `credentials.auto.tfvars` file.
 - Then saves the `id` of the server in `data.discord_server.createdServerInfo`.


&nbsp;


## Create a text channel

Now, we need to add a general channel for the server!

I found the provider requires a category channel needs created, and channels be placed under that category... so we can do this in one shot:

```tf
resource "discord_category_channel" "general" {
    depends_on = [
      data.discord_server.createdServerInfo
    ]
    name = var.category_name
    position = 0
    server_id = data.discord_server.createdServerInfo.id
}

resource "discord_text_channel" "general" {
    depends_on = [
      resource.discord_category_channel.general
    ]
    name = lower(var.text_channel_name)
    position = 0
    server_id = data.discord_server.createdServerInfo.id
    category = resource.discord_category_channel.general.id
}
```

### What does this do?

 - The block `discord_category_channel.general` creates a "general" category, which also depends on `data.discords_server.createdServerInfo` to contain information (ensuring the server is created before the category is)
   - This places the category in the server that we created by supplying its `id`
 - The block `discord_text_channel.general` creates a "general" text channel, which falls under the "general" category. Just like the category depends on the server to exist, this text channel depends on the category to exist.


&nbsp;


## Create an invite

Now, we need a way to actually see all of the work Terraform will perform. Since a [Bot](https://discord.com/developers/docs/intro#bots-and-apps) is what created the server and everything in it, we need an invite so our own user can join the server and interact with it.

So, create yet **another** file, call this one `createServerInvite.tf`:

```tf
resource "discord_invite" "inviteMe" {
    channel_id = resource.discord_text_channel.general.id
    max_age = 0
}

output "invite_info" {
    value = resource.discord_invite.inviteMe.id
}
```

### What does this do?

 - The `discord_invite.inviteMe` block will create a non-expiring invite code that we can use to join the server, placing the invite under the channel we created earlier (by `id`)
 - The `output.invite_info` block will print the invite code as a string to the terminal in a cleaner, more easily-found fashion.


&nbsp;


## Let Terraform do the work, and join your server!

Now, open a terminal session, `cd` into your Terraform files directory and run `terraform plan`.

 - This will show you what Terraform will do
   - You may have to correct some errors before you can proceed

Then, just run `terraform apply -auto-approve` to create the server.


&nbsp;


In the terminal, something like this will show:

```
Apply complete! Resources: 3 added, 0 changed, 0 destroyed. 

Outputs:

invite_info = "kvdyTVBuUs"
```


&nbsp;


This `invite_info` is what you need to join the server. To do this:

 - Open your Discord client
 - Click the "+" button under the list of joined servers to "Add a Server"
 - Under "Have an invite already?", click the "Join Server" button
 - Enter the code given by the terminal (you don't need a URL, just the code), and click "Join Server"!

You should now be a member of the new server, and you should also see a shiny new text channel titled `general`.