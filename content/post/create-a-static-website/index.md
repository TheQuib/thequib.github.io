---
author: "Quinn Henry"
title: "Create a Static Website"
date: "2022-03-28"
description: "Create a website with the Hugo static site generator."
tags: ["website", "website-building", "static-websites", "hugo", "linux", "windows"]
---

A static website is a plain and simple site with some HTML and CSS, without the need for a database or a beefy server.

Hugo is one of many different static site generators. The most popular is called [Jekyll](https://jekyllrb.com/), and is a bit simpler to use than Hugo. It's mostly personal preference, but here we'll be going through setup for Hugo.

&nbsp;

## Requirements

 - A computer running Windows / Mac / Linux / OpenBSD / FreeBSD
 - IDE that supports YAML and Markdown
 - Basic command line knowledge
 - Basic understanding of websites (HTML, CSS)

In this post, I'll be using Visual Studio Code as my IDE, you can download that [here](https://code.visualstudio.com/download) if you are interested.

&nbsp;

------

&nbsp;

# Install Hugo

Firstly, you'll have to install Hugo. This differs between operating systems... But the official instructions can be found [here](https://gohugo.io/getting-started/installing/).

Below, you can find instructions for Windows and Linux systems.


## Install on Windows

The best way to install on Windows is to use the [chocolatey](https://chocolatey.org/) method. You can find installation instructions for that [here](https://chocolatey.org/install#individual)

Once that is installed, run
```ps
choco install hugo
```


## Install on Linux

If you want to use Linux, I found it's best to get the latest version from Hugo's GitHub repo [releases](https://github.com/gohugoio/hugo/releases) page. If you try to install using apt, it will install an older version.

Download the latest release to a directory of your choosing

Ex:

```bash
wget https://github.com/gohugoio/hugo/releases/hugo_0.96.0_Linux-64it.deb
sudo dpkg -i hugo_0.96.0_Linux-64it.deb
```


## Confirm installation

To confirm you've installed Hugo, just run `hugo --version` in your command line

&nbsp;

------

&nbsp;

# Generate Hugo site files

To get started, will need to generate site files that you can edit

1. Open a terminal / command prompt
1. `cd` into an empty directory on your machine
1. Run `hugo new site sitename`
1. Run `ls` to view all of the new files

&nbsp;

------

&nbsp;

# Add theme to site

First thing you will need to do after creating a site is to add a theme to it. You can find a whole big list of themes directly from Hugo's website [here](https://themes.gohugo.io/).

There are two methods to add a theme to a site... Via a git submodule or just copying all theme files into your site.

In this example, I will be using the [PaperMod](https://github.com/adityatelange/hugo-PaperMod).


## Git Submodule Method

The benefit to cloning a submodule from git is you can quickly update the theme without having to re-copy all files manually. Git will take care of this for you :)

1. Find the theme you want, and go to its GitHub repository.
1. Copy the URL of the repository
1. Open a terminal session, `cd` in to your new site directory
1. Run: `git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod` *(substituting the URL of your chosen theme)*
1. To update the submodule, run `git submodule update --remote --merge`

*Note: If your site is in a git repository, you must run `git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod` to pull the submodule, as this is not done by default*


## Copy Method

You can also just copy the contents of the repository by clicking the "Code" button on the GitHub repo, extract the files from the downloaded `zip` file, and place those in your `themes` folder.

![GitHub Code Button](gitHubCodeButton.png)

&nbsp;

------

&nbsp;

# File structure

As you may have noticed, Hugo created a bunch of directories for you, including the `themes` directory from the last section.

The most notable and important directories are:

 - content
   - Storage for Markdown files that will be used to generate pages
 - layouts
   - Storage for HTML files that are used as templates to place markdown data into
 - static
   - Storage for all static files the website will use, such as CSS and image files
 - themes
   - Stores the theme you are using for your site

&nbsp;

------

&nbsp;

# Site configuration

Each site contains a configuration file, this can be either `toml` or `yaml` file types, and using one over another is dependent first on what your theme supports, and personal preference.

Typically, it is best to reference your chosen theme's documentation to get base configuration to get started.

The default `config.yml` file for PaperMod can be found [here](https://github.com/adityatelange/hugo-PaperMod/blob/exampleSite/config.yml). Feel free to make changes as you see fit.

&nbsp;

------

&nbsp;

# Create your first page

Now it is time to create your first page!

First create a file named `about.md` under the `content` directory.

Now, paste in the following lines:

```Markdown
---
title: "About"
description: "Description of page."
aliases: ["contact"]
author: "Your Name"
---
This article offers a sample of basic Markdown syntax that can be used in Hugo content files, also it shows whether basic HTML elements are decorated with CSS in a Hugo theme.

Xerum, quo qui aut unt expliquam qui dolut labo. Aque venitatiusda cum, voluptionse latur sitiae dolessi aut parist aut dollo enim qui voluptate ma dolestendit peritin re plis aut quas inctum laceat est.
```


Make sure to save the file.

&nbsp;

------

&nbsp;

# View your configured site

To spin up a quick webserver on your local machine, run the command `hugo server` in the directory containing your site files.

This will generate the website files, and serve an HTTP server locally on your machine (the output of the command will give you the url).

By default, Hugo will server on port `1313`, so you can open a web browser and go to http://localhost:1313

&nbsp;

Once you've done this, there is your new site! If you want to check out the `about.md` page we created above, you can navigate to http://localhost:1313/about.

&nbsp;

------

&nbsp;

# Extra note

If you want to generate all HTML files for this site, run the command `hugo -v`, this will create a directory named `public` and you can copy the files there to your webserver of choice.