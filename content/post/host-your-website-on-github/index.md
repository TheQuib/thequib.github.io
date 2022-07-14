---
author: "Quinn Henry"
title: "Host your website on GitHub"
date: "2022-07-06"
description: "GitHub pages provides a free hosting platform for static websites. It's a great place to get your first website going!"
tags: ["devOps", "cloud", "hugo", "linux", "static-websites", "website", "website-building"]
categories: ["Cloud", "Automation", "Hugo"]
image: featuredImage.png
---

## What is this?

GitHub pages is a static site hosting platform that hosts websites for free. It is available for free to anyone with a GitHub account, organization, and / or project.

With a little bit of static site generation using your favorite generator, and GitHub actions, your website will be up and running live on the internet in no time!

I will be using `Hugo` in this post, but it can be tweaked to accomodate for other site generators.


## 📝 Requirements

 - A GitHub account
 - GitHub desktop
 - An IDE to work in (such as Visual Studio Code)
 - A generated static site
   - Don't know where to get started? [I have a post on this](https://quibtech.com/p/create-a-static-website/)!

&nbsp;

## 🔍 Prepare GitHub

### 🚩 Create a repository

First, let's get started by creating the site's repository. There are a few requirements for this:
 - Must be your `username`, `organization` or `project` title and end with `.github.io`
   - An example for me would be: `thequib.github.io`
 - Make sure this is set to `public`
 
 - You can initalize a README if you would like
 - If you're using *Jekyll*, it may be a good idea to initalize with a `.gitignore`

#### ➕ Add gh-pages branch

Now, in your repository:

 - Click on the link titled `1 branch`

![](branchButton.png)

 - At the top right of the list, click `New Branch`
 - Name this branch `gh-pages`
 - Click `Create Branch`


### Change repository settings

Under the name of your repository:

 - Click the `Settings` tab

 ![](settingsButton.png)

 - On the left sidebar, click `Pages`
 - Click the `Branch` dropdown, and select your newly-created branch, *gh-pages*
 - Click `Save`


## 🔽 Clone the repository

Now, we'll get the repository down to the local machine to work on it. You can either do this in a GUI with [GitHub Desktop](https://desktop.github.com/) or by using the [Git CLI](https://git-scm.com/) with the following command:

```bash
cd directory/to/clone/to/
git clone https://github.com/YourUsername/yourRepository
```

## Add necessary files

### 🔼 Open in your IDE

 - It's super easy to work with a git repository in an IDE, such as Visual Studio code. But your favorite IDE should work anyways.
 - Open up the directory you cloned your repository to


### ➕ Add site files

It would be beneficial to add your hugo project files into this repository (if you are using Hugo). Again, other site generators are similar in workflows.


### ➕ Create your Actions workflow

A workflow in GitHub is an automation process defined by a `yaml` file. These can be anything from super simple, to extremely complex.

For GitHub Pages, it's pretty simple.

1. First, create a directory in the root of the repository titled `.github`
1. In this directory, create another directory titled `workflows`
1. In *this* directory, create a file titled `deploy-site.yml`
1. This `yaml` file will contain all of the instructions GitHub will use to deploy the site
   - An example of this file can be found below:

```yaml
name: Deploy Hugo

on:
  push:
    branches:
      - master
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

But, you can find tons of examples online if this doesn't suit your needs (such as with a different static site generator like Jekyll).

#### ❓ What will this action do?

1. In an Ubuntu 20.04 image
1. The repository will be checked out
1. Hugo will be set up, using the `extended` version in case needed
1. Site will be built using the command `hugo --minify`
1. Site will deploy to the repository, using the contents of the `./public` directory created from the previous command
   - In essence, the contents of `./public` are committed to the `gh-pages` branch
1. This will run a deployment action automatically that makes the site live


## ✅ Commit!

Now, let's get everything commited to GitHub, and your site should deploy!


## A few references

 - [Deploying Hugo with GitHub actions](https://gohugo.io/hosting-and-deployment/hosting-on-github/)
 - [GitHub actions documentation](https://docs.github.com/en/actions)
 - [This website's repository!](https://github.com/TheQuib/thequib.github.io)
   - As I use hugo, this contains the necessary actions workflow file and directory structure for Hugo