---
title: Netgear AC1900 WiFi USB Adapter install on Ubuntu 22.04
layout: post
---

I wanted to capture how I got the Netgear AC1900 WiFi USB Adapter(A7000) driver installed on Ubuntu 22.04.

The USB adapter comes with has a Realtek chipset and when plugginh in the adapter Ubuntu 22.04  recognizes it as rtl8814AU but does have a driver for it. Thanks to the website [Tutorial for Linux](https://tutorialforlinux.com/) I found a way to install the drivers via ppa (Ubuntu's Personal Package Archive).

The Real rtl8814AU Driver for Ubuntu PPA is developed thanks to the Free Work of Kelebek333.

You need to add the PPA repository first:

```
sudo add-apt-repository ppa:kelebek333/kablosuz && sudo apt -y update
```

Then you can install the driver like this:

```
sudo apt install rtl8814au-dkms
```

If not already installed you should install net-tools and wireless-tools:

```
sudo apt install net-tools wireless-tools
```

Since a new driver is being compiled during the install we should reboot to load it:

```
sudo reboot
```

Next we need to connect to a wifi network

Step 1: 

Run 'iwconfig' command to find the name of your wireless interface.

```
iwconfig
````
