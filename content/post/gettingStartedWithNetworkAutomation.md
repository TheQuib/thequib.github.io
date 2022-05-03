---
author: "Quinn Henry"
title: "Getting Started with Network Automation"
date: "2021-09-17"
description: "Ever thought about automating your networks?"
tags: ["networking", "gns3", "automation", "python"]
ShowToc: true
ShowBreadCrumbs: true
draft: false
---

Ever though about an easier way to configure tons of network devices easily? Python is definitely the answer to get started, and as long as you have a basic understanding of programming, it's pretty simple, and tons of fun!

<br>

------

<br>

## Requirements

 - Python version 3.9 or higher
 - An IDE that supports the Python language
 - Some sort of appliance you can connect to via SSH
   - I will be using a Cisco IOS appliance here running in GNS3
   - A Linux VM will also work

In this post, I'll be using Visual Studio Code as my IDE, you can download that [here](https://code.visualstudio.com/download) if you are interested.

You can find all of my [scripts](https://github.com/TheQuib/python-network-automation), and other projects on my [GitHub page](https://github.com/TheQuib).

<br>

------

<br>

# Before you Start

***Note:*** *This is only if you are using Visual Studio Code as your IDE*

VS Code gets a little funky when it comes to installed python libraries. First, make sure you install the latest version of Python from the Microsoft Store... Code will look to the installation path of this by default.

After installation:
1. Open VS Code
2. Open or create a Python file (use the extension *.py* to tell Code to use Python)
3. On the bottom-left of the window, click the Python version number, this should open a box at the top of the screen
4. Select "*Entire Workspace*"
5. Choose the following option from the list:

![Select Python Interpreter](/images/posts/gettingstartedwithnetworkautomation/selectInterpreter.png)

Now you should be all goood to go!

<br>

------

<br>

# Installing Netmiko

Netmiko is, in my opinion, the best all-in-one network automation tool to use in Python. Netmiko has a multitude of supported devices; including Linux, Cisco (IOS, NX-OS, etc), HP Enterprise, and TONS more.

To install Netmiko on your computer, open a terminal and type:

```bash
python3 -m pip install netmiko
```

Now, open your IDE and create a new Python file, enter the following code and run it to test installation:

```py
from Netmiko import ConnectHandler
```

If you run this, and it doesn't do anything, that's good! This just means that the library imported with no problems.

<br>

------

<br>

# Setting up Netmiko

Network automation is all about doing the similar tasks on tons of devices from a single place. That doesn't mean you have to have a hundred devices to do some automation work.

In my case, I will be using the Cisco Modeling Labs IOSv router image (in GNS3), so my commands will be IOS specific. I'll have some information down below so you can set up a Linux machine if you want as well :)

First, we'll need to import the ConnectHandler module from Netmiko:

```py
# NEtwork automation module
from netmiko import ConnectHandler
```

Then let's get some username and password information from the user:

```py
#Module used to hide passwords in the console
import getpass

#Ask user for username
username = input("Please enter a username to connect with:\n")
#Ask user for password using getpass
password = getpass.getpass("Please enter a password to connect with:\n")
#Ask user for secret password
secret = getpass.getpass("Please enter a secret password to enable with:\n")
```

Now we'll want to set up information that Netmiko will use for the actual connection. Type the following code and note the comments:

```py
#Defined host IP address, make sure this matches your machine
host = '1.2.3.4'

#Configuration dictionary that netmiko will use
hostConfig = {
    'device_type': 'cisco_ios',
    'host': host,
    'username': username,
    'password': password,
    #Used for exec priv commands if necessary
    'secret': secret,
    #Accept unknown SSH keys
    'use_keys': True
}

#Command to be sent to the machine
command = 'ip address'
```

This code creates a Python dictionary that contains the items *device_type*, *host*, *password*, *secret*, and *use_keys*, and then sets a variable to the string *'ip address'*.

Most of these are pretty self explanatory, *use_keys* will bypass the prompt SSH gives back when connecting to an unknown device for the first time.

If you wanted to try this on multiple devices, tweak your code to use a list for hosts and a for loop as such:

```py
#Defined host IP address, make sure this matches your machine
hosts = [
    '1.2.3.4',
    '4.3.2.1'
]

for host in  hosts:
    #Configuration dictionary that netmiko will use
    hostConfig = {
        'device_type': 'cisco_ios',
        'host': host,
        'username': username,
        'password': password,
        #Used for exec priv commands if necessary
        'secret': secret,
        #Accept unknown SSH keys
        'use_keys': True
    }
    #Command to be sent to the machine
    command = 'sh run'
```

# Sending your first command

Now we're ready to send a command to your device / devices! Let's get started by creating a Netmiko `ConnectHandler` variable by adding this line:

```py
ssh = ConnectHandler(**hostConfig)
```

Now, we can act on the variable *ssh* to send commands to our device. Using *send_command*, which is used specifically for sending a single command via Netmiko (this will utilize the *command* variable we set in the previous step):

```py
output  = ssh.send_command(command)
print(output)
```

Now go ahead and run your program! If you put everything in correctly, you should see the output of the Linux command `ip addr` in your terminal.

<br>

------

<br>

# Sending multiple commands

Automation isn't just about sending a single command to devices. While this can be pretty useful, especially for gathering some information (like switch configs), sending multiple commands can prove to be extremely useful and time-saving, especially when you're configuring tons of devices.

In the previous step, we used the function **send_command()**, which only sends one command. For multiple configuration commands, there is a function called **send_config_set()**, which accepts a list in its parameters.

So first, let's create a list:

```py
config = ['int gi0/1', 'ip address 172.16.0.1 255.255.0.0']
```

Now, instead of *send_command()*, we can use the following to send our *config* variable instead:

```py
ssh.send_config_set(config)
```
