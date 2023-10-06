---
title: How to set up Selenium Python and GeckoDriver on a Raspberry Pi 4 (2023)
subtitle:

# Summary for listings and search engines
summary: Set up your development environment to do browser automation on a Raspberry Pi

# Link this post with a project
projects: []

# Date published
date: "2023-10-06T00:00:00Z"

# Date updated
lastmod: "2023-10-06T00:00:00Z"

# Is this an unpublished draft?
draft: false

# Show this page in the Featured widget?
featured: false

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Selenium, Python, and Firefox logos'
  focal_point: ""
  placement: 1
  preview_only: false

authors:
- admin

tags:
- Raspberry Pi
- Python
- Selenium
- GeckoDriver

categories:
- Tech
---

## Table of Contents
 
1. [Introduction](#introduction) 
2. [Install Firefox](#install-firefox)
3. [Install Python/PIP](#install-python-pip)
4. [Install Selenium](#install-selenium)
5. [Install GeckoDriver](#install-geckodriver)
6. [Create at test script](#create-test-script)   

## <a id="introduction"></a>Introduction

Last week, I created a small python script to automate a task for my partner. 

I’ll write a detailed blog post about it but the task involved logging on a website and doing a bunch of stuff at a specific time. After doing some research, I decided to use python and Selenium to do that, and more specifically the [Selenium Webdriver](https://www.selenium.dev/documentation/webdriver/). 

What is Selenium? It’s an open-source software to do browser automation. It runs on Windows, Linux, and macOS and supports JavaScript (Node.js), C#, Groovy, Java, Perl, PHP, Python, Ruby and Scala. The Selenium WebDriver accepts commands and sends them to a browser. This is implemented through a browser-specific browser driver, which sends commands to a browser and retrieves results.

Since my main laptop is on Windows 10, I installed the latest version of the Windows Subsystem for Linux (WSL), installed Ubuntu and the latest versions of Firefox, Selenium, and GeckoDriver as I wanted to use the Firefox browser for this project. You can also use Chrome, Edge, IE, and Safari but note that each of them need a [specific WebDriver](https://www.selenium.dev/documentation/webdriver/browsers/) to be able to understand the commands you’re going to send to them via your script.

> With previous versions of the WSL, you needed to [install an X server](https://www.gregbrisebois.com/posts/chromedriver-in-wsl2/) to run GUI apps such as Firefox. This is not needed anymore as Microsoft released WSLg ([blog post](https://devblogs.microsoft.com/commandline/the-initial-preview-of-gui-app-support-is-now-available-for-the-windows-subsystem-for-linux-2/), [documentation](https://learn.microsoft.com/en-us/windows/wsl/tutorials/gui-apps)).
> 

After a couple of days of writing code and doing some testing, the script was done and worked very well but I didn’t want to keep my laptop on all night just to run it so I decided to use my Raspberry Pi 4 which is already sitting on my desk and running 24/7.

My first thought was that I just needed to install Firefox, Selenium, and Geckodriver and then create a cron job so it can run at a specified hour, simple right? Well, not exactly. You’ll see why below but, in short, it’s because the Raspberry Pi 4 runs on an ARM platform and because there are a few incompatibilities between Firefox, Selenium, and GeckoDriver.

## <a id="install-firefox"></a>Install Firefox

Depending on the operating system you have installed on your Raspberry Pi, you might need to install a different version of Firefox and it’s because the GeckoDriver doesn’t like when Firefox is running in a container (e.g. snap, flatpak).

When Firefox is packaged inside a container (like the default Firefox browser shipped with Ubuntu 22.04), it may see a different filesystem to the host. This can affect access to the generated profile directory, which may result in a hang when starting Firefox. Workarounds are listed in the geckodriver [usage documentation](https://firefox-source-docs.mozilla.org/testing/geckodriver/Usage.html#Running-Firefox-in-an-container-based-package).

That issue is known by the Mozilla team and they are currently working on it ([GeckoDriver documentation](https://firefox-source-docs.mozilla.org/testing/geckodriver/Usage.html#Running-Firefox-in-an-container-based-package), [Github Issue](https://github.com/mozilla/geckodriver/issues/2055), [Bugherd](https://bugzilla.mozilla.org/show_bug.cgi?id=1819377)).

### Raspberry Pi OS

On the Raspberry Pi OS (raspbian), only Firefox ESR (Extended Support Release) is available. That version is not running in a container so you won’t have the above issue but you’ll want to make sure the versions of Firefox, GeckoDriver, and Selenium and aligned with the mapping created by Mozilla.

To install Firefox ESR, update the package list

```
sudo apt update
```

Install the latest version

```
sudo apt install firefox-esr
```

![Compatiblity matrix between Firefox, Selenium and GeckoDriver](https://nicolaslouge.com/post/how-to-set-up-selenium-python-geckodriver-raspberry-pi-arm-2023/firefox-geckodriver-selenium-compatibility.png "Compatiblity matrix between Firefox, Selenium and GeckoDriver")

### Ubuntu 22.04+

Ubuntu 22.04 and later comes with the Firefox snap package by default. It’s the only version included out-of-the-box and even if you run `apt install firefox` it will install the snap version and not the .deb one.

According to the [GeckoDriver documentation](https://firefox-source-docs.mozilla.org/testing/geckodriver/Usage.html#Running-Firefox-in-an-container-based-package), there are three options:

- Do not use container-packaged Firefox builds with geckodriver.
- Use a geckodriver that runs in the same container filesystem as the Firefox package. For example on Ubuntu `/snap/bin/geckodriver` will work with the default Firefox.
- Set the `-profile-root` command line option to write the profile to a directory accessible to both Firefox and geckodriver, for example a non-hidden directory under `$HOME`.

For simplicity, I preferred to use the first one which meant I had to remove the snap version of Firefox that was already installed and then had to installed the .deb version. To do so, follow the five steps on this blog post: [How to Install Firefox as a .Deb on Ubuntu 22.04 (Not a Snap)](https://www.omgubuntu.co.uk/2022/04/how-to-install-firefox-deb-apt-ubuntu-22-04).

## <a id="install-python-pip"></a>Install Python/PIP

Python is already installed by default on Ubuntu 22.04 but if it’s not installed on your linux distro, you can install it by running the following command.

```
sudo apt install python3
```

You also need to install pip, a package installer for python
```
sudo apt install python3-pip
```

When the installation is complete, verify the installation by checking the pip version:

```
pip3 --version
```

## <a id="install-selenium"></a>Install Selenium

To install the latest version of selenium, run the following pip command

```
pip install selenium
```

To find out which version of Selenium you have installed

```
python3 -c "import selenium; print(selenium.__version__)"
```

You can find more information on the [selenium Pypi.org page](https://pypi.org/project/selenium/).


## <a id="install-geckodriver"></a>Install GeckoDriver

Before jumping into the installation steps, we need to talk about central processing unit (CPU) architecture and operating systems.

As you can see in the table below, all the Raspberry Pis that have been released to date feature a Broadcom system on a chip (SoC) with an integrated ARM central processing unit (CPU).

![Raspberry Pi Architecture Table](https://nicolaslouge.com/post/how-to-set-up-selenium-python-geckodriver-raspberry-pi-arm-2023/raspberry-pi-architecture.png "Raspberry Pi Architecture Table")

**Raspsberry Pi 1** = **32-bit** central processing unit (CPU) that implement the **ARMv6** instruction set architecture.

**Raspberry Pi 2 Model B v1**= **32-bit** central processing unit (CPU) / **ARMv7** instruction set architecture.

**Raspberry Pi 2 Model B v1.2** to the **Raspberry Pi 4 Model B**= **64/32-bit** central processing unit (CPU) / **ARMv8** instruction set architecture.

When I first set up my Raspberry Pi 4, I installed the 32-bit version of the Raspberry Pi OS (raspbian). However, the Mozilla team stopped providing an arm7hf version after v[0.23.0](https://github.com/mozilla/geckodriver/releases/tag/v0.23.0) in January 2019 but they recently started to provide an aarch64 version since v.32.0 in October 2022.

Interesting bit from the [ARM Wikipedia page](https://en.wikipedia.org/wiki/ARM_architecture_family#Armv8-A):

> AArch64 is the 64-bit state introduced in the [Armv8-A architecture](https://en.wikipedia.org/wiki/ARM_architecture_family#Armv8-A). AArch64 provides user-space compatibility with Armv7-A, the 32-bit architecture, therein referred to as "AArch32". Armv8-A allows 32-bit applications to be executed in a 64-bit OS.
> 

What it means:

- If you have a 64-bit OS: you can install the aarch64 version of GeckoDriver and you could potentially install the arm7hf version
- If you have a 32-bit OS: you can install the arm7hf version only but they stopped providing new versions of the driver. Mozilla’s answer to this issue is “compile it yourself” but fortunately some people have already done it. You can download the unofficial arm7hf package on James Mortensen’s repository on Github: **[geckodriver-arm-binaries](https://github.com/jamesmortensen/geckodriver-arm-binaries)**

What I did:

- I completely wiped my Raspberry Pi 4 and installed **[Ubuntu Desktop 22.04.3 LTS](https://ubuntu.com/download/raspberry-pi)** for the simple reason that I only found James’s repo after the fact. It turned out great but since I have the 2GB version of the Pi4, I don’t get the best experience as Ubuntu consumes almost 1.5 GB of RAM just to show the desktop. Moving forward, I’ll install Ubuntu Server 22.04.3 LTS ****or the **[Raspberry Pi OS 64-bit](https://www.raspberrypi.com/software/operating-systems/)** that was released earlier this year.

Whether you have a 64-bit/32-bit OS, don’t use python libraries like **[webdriver-manager](https://pypi.org/project/webdriver-manager/)** because they [don’t recognize and download binaries for an ARM architecture](https://github.com/SergeyPirogov/webdriver_manager/issues/200).

Now that you know which version to get based on your own hardware we can download GeckoDriver

```
wget https://github.com/mozilla/geckodriver/releases/download/v0.33.0/geckodriver-v0.33.0-linux-aarch64.tar.gz
```

Install GeckoDriver

```
sudo tar -xzvf geckodriver-v0.33.0-linux-aarch64.tar.gz -C /usr/local/bin
```

Make it executable

```
chmod +x /usr/local/bin/geckodriver
```

Check version

```
geckodriver -V
```

## <a id="create-test-script"></a>Create a test script

```python
from selenium import webdriver

service = webdriver.ChromeService(executable_path='/usr/local/bin/geckodriver')
driver = webdriver.Firefox(service=service)

driver.get("https://duck.com/")
```

If you get an `OSError: [Errno 8] Exec format error` or some variants of it (see the two examples below), it’s because you haven’t installed the correct version of the driver for your system.

- `OSError: [Errno 8] Exec format error: '/home/nicolas/.local/lib/python3.10/site-packages/selenium/webdriver/common/linux/selenium-manager’`
- `bash: /usr/local/bin/geckodriver: cannot execute binary file: Exec format error`

If you did install the correct version of the driver but get an **`Unable to Locate Driver`** error, it’s because you need to use a [service class](https://www.selenium.dev/documentation/webdriver/drivers/service/) and specify the path of the driver because the ARM architecture is not well supported by Selenium (Github issues [11599](https://github.com/SeleniumHQ/selenium/issues/11599#issuecomment-1702617867)/[11356](https://github.com/SeleniumHQ/selenium/issues/11356#issuecomment-1666412800)).

On WSL (running x86_64), I just needed the following to do the same thing and Selenium was able to find the executable path by itself.

```
from selenium import webdriver

driver = webdriver.Chrome()
driver.get("https://duck.com/")
```

This is what you should see after running the script. The little robot emoji next to the URL means the browser is controlled by the GeckoDriver.

![Firefox controlled by Selenium/GeckoDriver](https://nicolaslouge.com/post/how-to-set-up-selenium-python-geckodriver-raspberry-pi-arm-2023/firefox-marionette-selenium-geckodriver.png "Firefox controlled by Selenium/GeckoDriver")