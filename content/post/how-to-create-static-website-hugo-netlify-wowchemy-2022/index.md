---
title: How to create a static website with Hugo, Netlify and Wowchemy (2022)
subtitle:

# Summary for listings and search engines
summary: Do you want to create your own website? In this post, you'll learn everything you need to know on how to do it step-by-step with Hugo, Netlify and Wowchemy.

# Link this post with a project
projects: []

# Date published
date: "2022-05-11T00:00:00Z"

# Date updated
lastmod: "2022-05-11T00:00:00Z"

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: false

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Hugo, Netlify, and Wowchemy logos'
  focal_point: ""
  placement: 1
  preview_only: false

authors:
- admin

tags:
- Hugo
- Netlify
- Wowchemy

categories:
- Tech
---

## Table of Contents

1. [Introduction](#introduction)   
2. [What's what](#whatswhat)   
   1. [What's a static website?](#staticwebsite)    
   2. [What's a static site generator?](#sitegenerator)   
   3. [What's the Jamstack?](#jamstack)   
3. [Where do I start?](#wheredoistart)      
4. [Set up your development environment](#setup)   
5. [Deploy the Academic theme](#deploy)   
6. [Make it yours](#makeityours)     
   1. [How to remove the green - demo - section](#howtoremovedemo)   
   2. [How to hide any section](#howtohidesection)   
   3. [How to edit the top navigation bar](#howtonav)   
   4. [How to update the favicon (site icon)](#howtofavicon)   
   5. [How to update the avatar](#howtoavatar)   
   6. [How to update the name, position, social icons, and biography](#howtonamepositionsocialbio)   
   7. [How to customize a section](#howtocustomize)   
7. [Update](#update)
8. [Resources](#resources) 

## Introduction

If you want to create a website or a blog using a static site generator (SSG), there are a lot of different guides about this subject on the internet. Some are recent, some are older, but a lot of them are not really detailed to start from scratch. For the most part, they are easy to follow along but they're going straight to the point fast, sometimes too fast. If you're like me, you might always end up having more questions about the tools and the logic behind it all than before you started reading up on it. 

This is why I've created this "ultimate guide" on how to set up a website from scratch with Hugo, Netlify, and Wowchemy. I hope it will help you get a better understanding of the entire process and of the technologies being used. 

Let's dive in!

## <a id="whatswhat"></a>What's what?

### <a id="staticwebsite"></a>What's a static website?

Modern websites are separated in two types: static and dynamic.

Static websites are made up of HTML pages that load the same way every time for every visitors. There is no database queries and no PHP processing. The code hosted on your web server is the same code the browser will see when requesting to load your website.

Dynamic websites are rendering the webpage at the time of the request. The code the browser will see when requesting to load the site will be one that has been generated on the fly by the web server. They are more powerful but also more complex and expensive to set up, run, and maintain.

### <a id="sitegenerator"></a>What's a static site generator?

To close the gap between static and dynamic websites, static site generators (SSGs) have stepped in. These tools create HTML pages with HTML templates, text files ([Markdown](https://en.wikipedia.org/wiki/Markdown)), and configuration files. The difference is, the process happens only once during the build stage instead of every time someone is requesting to see your website.

[Hugo](https://gohugo.io/) is a static site generator written in Go that was originally created by Steve Francia in 2013. [^1] In 2015, it started to quickly gained a lot of popularity along other SSG like [Jekyll](https://jekyllrb.com/).

In 2016, Matt Biilmann and Chris Bach (the co-founders of [Netlify](https://www.netlify.com/)) came up with the name "JAMstack" [^2] to "better define what developers were already starting to do which was to "decouple the front- and back-end of web and apps, focus on best practices of speed and availability, and redefine their workflows". [^3]

### <a id="jamstack"></a>What's the Jamstack?

Originally, `JAM` stood for JavaScript, API & Markup and `stack` for all the technologies used.

![Illustration of the Jamstack (from Jamstack.wtf)](https://nicolaslouge.com/post/how-to-create-static-website-hugo-netlify-wowchemy-2022/jamstack.png "The Jamstack (Jamstack.wtf)")

In 2020, "JAMstack" became "Jamstack" and the name is now used to broadly refer to an architectural approach for building websites that are decoupled (where the front end is built from a static site generator while the back end is often integrated with the front), static first, and progressively enhanced.

Jamstack sites are:
- Fast
- Secure
- Less expensive
- Scalable

If you want to read more about Jamstack and its history, go to [Jamstack.wtf](https://jamstack.wtf/).

## <a id="wheredoistart"></a>Where do I start?

For starters, you need to choose the static site generator tool you'll use to create and build your website. There are more than 300 generators to choose from and new ones are seeing the light almost every day. You can see the full list on the [Jamstack](https://jamstack.org/generators/) website. The top 3 are: Next.js, Hugo, and Gatsby.

![Screenshot of Jamstack.org](https://nicolaslouge.com/post/how-to-create-static-website-hugo-netlify-wowchemy-2022/ssg-jamstack.jpg "List of Static Site Generators (Jamstack.org)")

For this guide, we'll use Hugo with the Academic-resume theme from [Wowchemy](https://wowchemy.com/).

## <a id="setup"></a>Set up your development environment

The first step is to set up your local development environment. I'm on the latest Windows 10 build and the following section will detail how to install the software we need on this operating system only. If you're on macOS or Linux, feel free to adapt the next steps  and find alternatives that are available on your OS.

1. Windows Terminal

The first tool we'll download is the new Windows Terminal. It's open-source, modern, and it has a lot of settings and configuration options. We'll need to use it to launch specific commands to run your Hugo website locally. To install it, go to the [Microsoft Store](https://www.microsoft.com/en-ca/p/windows-terminal/9n0dx20hk701?activetab=pivot:overviewtab) and install it from there. This method allows you to always be on the latest version when Microsoft releases new builds.

2. Hugo

Hugo, the static website generator we'll use for this project, is available on multiple platforms (macOS, Windows, Linux) and in different versions.

Why do we need to install it locally? To build (generate the files of) our website and see the changes instantly without having to push the code on the internet.

To install it on Windows, follow the [detailed guide](https://gohugo.io/getting-started/installing/#less-technical-users) on the Hugo website and **make sure you're installing the `extended version`** (when I first installed it back in November 2021, the latest version was "hugo_extended_0.89.4_Windows-64bit.zip").

Why do we need to use the `extended` version? Because the Hugo theme we'll be using [processes `SCSS` to `CSS`](https://gohugo.io/troubleshooting/faq/#i-get-tocss--this-feature-is-not-available-in-your-current-hugo-version) for the look and feel of the website and the Hugo documentation says the extended version is needed for that.

Once it's installed, reboot your machine. If you need to see if it's installed correctly, launch Windows PowerShell in the Windows Terminal and type:

```
hugo help
```

You should see the following output:

```
hugo is the main command, used to build your Hugo site.

Hugo is a Fast and Flexible Static Site Generator
built with love by spf13 and friends in Go.

Complete documentation is available at http://gohugo.io/.
```

3. Go & Git

To run correctly, Hugo [needs](https://gohugo.io/hugo-modules/use-modules/#prerequisite) to have two other tools installed. The first one is the Go compiler toolchain (as Hugo is written in Go) and the second one is Git (a free and open source distributed version control system that will help you manage and track all the changes made on your code).

To install Go, go to [go.dev](https://go.dev/) and download and [install](https://go.dev/doc/install) the binary for Microsoft Windows in the "Featured downloads" section. To verify that you've correctly installed Go, open the Windows Terminal and type:

```
go version
```

If it's installed, you should see a similar output:

```
go version go1.17.3 windows/amd64
```

To install Git, go to [git-sm.com](https://git-scm.com/downloads) and download and install the latest release for Windows. To make sure that you've installed git, open the Windows Terminal and type:

```
git version
```
If it's installed correctly, you should see a similar output:

```
git version 2.33.1.windows.1
```
3. Visual Studio Code

To edit the files of your website you need a text editor and one of the best on Windows, if not the best at the moment, is Visual Studio Code.

To install Visual Studio Code, go to [code.visualstudio.com](https://code.visualstudio.com/) and click on the blue button that says "Download for Windows".

Once installed, you now have everything you need to create and run your Hugo static website locally on your machine. To push it live on the internet, you'll need to create an account on two websites. The first one is Github and the second one is Netlify.

4. Github

[Github](https://github.com/) is a website where developers can store their projects on public or private locations (called repositories). The site offers the distributed version control and source code management (SCM) functionality of Git, plus its own features.

To create an account, go to [Github](https://github.com/) and click on the sign up button. The name of your account will be the name of your profile (mine is [github.com/nicolaslouge](https://github.com/nicolaslouge)).

5. Netlify

Netlify is a cloud company founded in 2015 by Matt Biilmann and Chris Bach. They came up a year later with the name "JAMstack". Their website offer free hosting for static websites (with some limitations) under the "starter" plan.

The Github repository (project) you created for your website will be linked to your Netlify account. It means you'll be able to store your website code for free on Github and then deploy and host it on Netlify automatically, also for free.

To create an account go to [Netlify](https://www.netlify.com/) and click on sign up at the top right of the site. Since you've already created a Github account in the above step, you can easily create a Netlify account by using your Github credentials.

## <a id="deploy"></a>Deploy the Academic theme

Now that you're all set up, we'll deploy the Academic theme template on our Github/Netlify environment. On the [Wowchemy](https://wowchemy.com) website, click on ```Get Started``` on then on [Start with Academic Resume](https://app.netlify.com/start/deploy?repository=https://github.com/wowchemy/starter-hugo-academic&stack=cms).

![Screenshot of the "Get Started" page on the Wowchemy.com site](https://nicolaslouge.com/post/how-to-create-static-website-hugo-netlify-wowchemy-2022/wowchemy-start.jpg "Screenshot of the "Get Started" page on Wowchemy.com")

A Netlify page will open up asking you to connect to Github and then to give a name to the new repository that Github will create for you. Mine is named "nicolaslougecom" but you can name it anything you'd like. For this demo, I'll use ```hugo-academic-demo```. You can then click on ```Save & Deploy```. 

Behind the scenes, Github will clone the ```starter-hugo-academic repository``` on your account under the name you chose and Netlify will create a new project/site that is linked to your repository and will give you a public URL which means your website is already live on the internet! Anytime, you'll push new code in this repository, Netlify will generate the HTML and CSS files and will host them on their servers.

On your Netlify account, you can see you have a new site. Click on it, and you'll see the URL Netlify generated for your project under ```Site overview```.

![Site overview on Netlify](https://nicolaslouge.com/post/how-to-create-static-website-hugo-netlify-wowchemy-2022/netlify-siteoverview.jpg "Site overview on Netlify")

## <a id="makeityours"></a>Make it yours

Now that your site is live, it's time to make it yours and to edit the code. To get the code that is in your Github repository, you need to clone it. To do so, open your file explorer and create a new folder named ```Github``` wherever you like but preferably in a location that is easy to access. Then, open Visual Studio Code on your machine, click on ```Terminal``` on the top menu bar and then ```New Terminal``` and enter the following cd command followed by the path name of where you create your folder. Mine is under ```Documents``` so my command will look like this:

```
cd C:\Users\Nicolas\Documents\Github
```

Now, go to your Github account, click on your website repository, click on the green Code button and then copy the HTTPS address that Github gives you. Go back to your terminal in Visual Studio code and run the following command with the address you have just copied:

```
git clone https://github.com/nicolaslouge/hugo-academic-demo.git
```

The code of your website is now in the Github folder you created on your machine under the name of your website repository. In Visual Studio, click on ```File```, ```Open Folder```, and open your project. This is what you should see:

Now that you have all the files locally, you can run a few commands to start a web server on your machine and see instantly the changes you'll make without having to push the code on Github. Only you will see the changes and your live website will not change (not until you push the new code).

To run the web server, open the Windows Terminal and run the following commands:

```
cd C:\Users\Nicolas\Documents\Github\hugo-academic-demo
hugo server
```

Hugo will generate the static files (HTML, CSS) of your websites (which you can find in the ```Github\hugo-academic-demo\static\uploads``` folder) and will then start a local web server. You can see in your terminal the address of the local website. Any changes you'll make will be instantly reflected. For now, the website is the exact same as the one hosted on Netlify. 

If you get an error similar to ```hugo server Error: from config: failed to resolve output format "headers" from site config```, go to the ```%TMP%\hugo_cache\``` folder on Windows and delete the ```modules```  folder. Then, launch the ```hugo server``` command again ([see the thread on the Hugo forum](https://discourse.gohugo.io/t/site-works-on-netlify-but-not-locally-error-from-config/34614)).

![Screenshot of a PowerShell running the hugo server command](https://nicolaslouge.com/post/how-to-create-static-website-hugo-netlify-wowchemy-2022/hugo-server.png "Screenshot of a PowerShell running the hugo server command")

Alright, this is where it gets more complicated. The Academic theme source code (which is now your website) is divided between multiple folders.

```assets``` (contains your website favicon)   
```config``` (contains four configuration files)   
```content``` (contains the different modules that are on your website)   
```data``` (contains configuration file of the page sharer social icons you can enable/disable)   
```exampleSite``` (is an example of a site with some content, you can delete this folder)   
```images``` (contains two images)    
```resources``` (contains .webp generated version of the theme images to make the site faster)   
```scripts``` (contains a script to reset your site)   
```static``` (contains the static files generated by Hugo when you run the hugo command)   

Not everything you'll visually see in one section on the website can to be updated in only one file. Take some time to open every file in every folder and skim through the code. Try to understand it but if it doesn't make sense right away, don't worry too much about it.

Next, you can read and follow the steps on the [Hugo Site Customization](https://wowchemy.com/docs/getting-started/customization/) and/or follow the how-tos below.

### <a id="howto"></a>How-to

#### <a id="howtoremovedemo"></a>How to remove the green - demo - section

Go to the ```content/home/``` folder and delete the ```demo.md``` file.

#### <a id="howtohidesection"></a>How to hide any section

If you want to remove a specific section (like the Skills or Experience one), you can add the following code at the top of the ```skills.md``` or ```experience.md``` file in the ```content/home/```

```
# Activate this widget? true/false
active: false
```

#### <a id="howtonav"></a>How to edit the top navigation bar

```Academic``` is the site title and can be changed in the ```config/_default/config.yaml``` file.   
```Home```, ```Posts```, are the different menu options and can be changed in the ```config/_default/menu.yaml``` file.   
The search and the theme picker can be enabled/disabled in the ```config/_default/params.yaml``` file.   

#### <a id="howtofavicon"></a>How to update the favicon (site icon)

Add your new .png icon in the ```assets/media``` folder and rename it ```icon.png```. You can remove the old one.

#### <a id="howtoavatar"></a>How to update the avatar

Add your new avatar in the ```content/authors/admin``` folder and rename it ```avatar.jpg``` (if your image is a JPG). It should be a square image with the same height/width to get the best result. You can remove the old one.

#### <a id="howtonamepositionsocialbio"></a>How to update the name, position, social icons, and biography

You can edit all of those in the ```_index.md``` file in the ```content/authors/admin``` folder.

#### <a id="howtocustomize"></a>How to customize a section

If you want to customize a section, like the biography one and add your ```Certifications``` next to your ```Education```, then you'll have to edit the HTML template Hugo is using when generating your site.

This is done through partials and the ones of your site template can be seen in the [wowchemy-hugo-themes](https://github.com/wowchemy/wowchemy-hugo-themes/tree/fda9f39d872ec066b4f11a9c68b85aac2163bf75/wowchemy/layouts/partials) repository.

To add the ```Certifications``` section on my site, I had to:

1. Identify which partial I had to modify by inspecting the source of the ```Education``` section on my website and then search in the above repository in which file the code was (it was in the ```about.html``` file).
2. Create a file named ```about.html``` under the ```layouts\partials\widgets\``` folder and copy/paste the content of the ```about.html``` partial in the wowchemy repository.
3. Create a ```Certifications```` section

You can see the entire process [here](https://github.com/nicolaslouge/nicolaslougecom/issues/1).

Warning! Don't just copy and paste my code as my website is not up to date with the latest version. Use the code that match the theme version of your website. For example, I created my site in November 2021, so I had to find the correct version of the partial file under the [file history on Github](https://github.com/wowchemy/wowchemy-hugo-themes/commits/4b9ee3021cb4b9c085d318692ff71478b5d6abb5/wowchemy/layouts/partials/widgets/about.html).

## Update

When a new version of the template is released by Wowchemy, you'll have to update your site. I haven't done it myself yet, but you can see the process on the [Update](https://wowchemy.com/docs/hugo-tutorials/update/) page on the Wowchemy site.

## Resources

[^1]: https://en.wikipedia.org/wiki/Hugo_(software)
[^2]: https://jamstack.wtf/#about
[^3]: https://sdtimes.com/webdev/jamstack-brings-front-end-development-back-into-focus/
[^4]: https://www.kyleichan.com/post/hugo-academic/
[^5]: https://www.rohitsalecha.com/post/create_a_personal_blog_using_hugo_academic_netlify/
[^6]: http://conormclaughlin.net/2017/11/deploying-a-static-website-to-s3-using-hugo/
[^7]: https://isabella-b.com/blog/hugo-academic-customization/
[^8]: https://christophergmyers.net/