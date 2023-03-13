---
title: "(LiM) Kubernetes Cluster set up using Ansible"
layout: post
published: true
---

Inspired by “Network Chuck”’s video tutorial[ “i built a Raspberry Pi SUPER COMPUTER!! // ft.
Kubernetes (k3s cluster w/ Rancher)” on youtube](https://www.youtube.com/watch?v=X9fSMGkjtug){:target="_blank"} and corresponding course on his [website](
https://learn.networkchuck.com/courses/take/raspberry-pi-projects/lessons/29763478-i-built-a-
raspberry-pi-super-computer-ft-kubernetes-k3s-cluster-w-rancher/discussions/index){:target="_blank"} as well as Jeff Geerling’s [video channel](https://www.youtube.com/@JeffGeerling){:target="_blank"} and [blog](https://www.jeffgeerling.com/blog){:target="_blank"}, I set out on four journeys:

1. The first led me down my father’s foot steps of electrical engineering designing and building a custom carrier board for the Raspberry Pi compute module.
2.  The second, learning and creating kubernetes clusters on Raspberry Pis with [kubernetes](https://kubernetes.io/){:target="_blank"}/[k3s](https://k3s.io/){:target="_blank"} - a light weight version of kubernetes and managing them with [Rancher](https://www.rancher.com/){:target="_blank"}. 
3.  The third, learning a new to me way of automating/simplifying administrative tasks, cluster installs and management using [Ansible](https://www.ansible.com/){:target="_blank"}.
4.  The fourth, was designing a cluster tray that would house my cluster in a unique way, inspred by toilet paper roles and a Pringles tube. But more about that later.

I should add that I am well down the path of my career but I always enjoyd tech and Linux. However, I am doing all of this as a hobby. I am sure there is many different (and possibly better) ways of doing things but this was my way.
# Journey 1 - Developing the LiM Cluster CM4 Carrier Board
Watching  Jeff Geerling's video [New Raspberry Pi Projects - CM4 NAS, Piunora, and Seaberry!](https://www.youtube.com/watch?v=7Li7Nh9V74I&t=300s){:target="_blank"} where he talks about [mebs_t's self designed NAS](https://github.com/mebs/CM4-NAS){:target="_blank"}, made me think I am interested in doing somehting like this too. At the time, the use case I was woking on was [staking Navcoin headlessly with a Raspberry Pi Compute Module 4.](/2021/06/13/navcoin-staking/){:target="_blank"}.

I was looking for a Raspberry Pi Compute Module 4 (CM4) carrier board that only provides power to a CM4 with emmc storage. Many of the carrier boards out at the time provided a lot more functionality so set out to design and build the [Less-is-More (LiM) carrier board](https://lim.loonix.ca/pages/LiM_Board.html){:target="_blank"}.
## The RPi CM4 - LiM Carrier Board series

The LiM Carrier Board series is a series of minimalistic Raspberry Pi (RPi) Compute Module 4 (CM4) Carrier Boards. Less-is-More (LiM) refers to the minimalistic design only providing the most rudimentary functionality to the CM4 such as 5V power via USB-C power connector and two status (power/activity) LEDs for the original LiM Carrier Board version. Additional + versions of the LiM Carrier Board will/might feature additional functionality such as flashing capability and SD card slot (see conceptual table below).

Except for the LiM CM4 Cluster Carrier Board, the LiM and LiM+ Carrier boards are meant for CM4 boards with onboard storage (the LiM CM4 Cluster Carrier Board supports both CM4 versions with and w/o eMMC (lite version), micro SD card and 2232 and has a 2241 M.2 M-key socket for NVMe PCIe SSD) and wifi as the LiM and LiM+ carrier boards provides no other functionalty other than power and status LEDs. The LiM CM4 Cluster Carrier Board has PoE ethernet and supports wifi and non-wifi CM4 models.C

{% include carousel.html height="75" unit="%" duration="7" number="1" %}
_**Various LiM Carrier Board models**_

Three versions of the LiM Carrier Board series have been prototyped and more information can be found on their respective pages 

- [LiM Carrier Board](pages/LiM_Board.html)
- [LiM+ Carrier Board](pages/LiM+_Board.html)
- [LiM CM4 Cluster Carrier Board Carrier Board](pages/LiM_Cluster_CB.html)

The original LiM carrier board took about two months from idea (May 22nd, 2021) to delivery (July 14th, 2021). Special thanks to [Anish Verma](https://www.linkedin.com/in/anish-verma-a5a05a8b/){:target="_blank"} for working with me on the CAD designs of the carrier board.

I was looking for such a minimalistic board and had a conversation with [Jeff Geerling](https://www.jeffgeerling.com/){:target="_blank"} when I asked him if he knew of a more minimalistic design than [this board](https://www.tindie.com/products/dronecz/minimal-carrier-board-for-compute-module-4/){:target="_blank"}? He pointed me to to uptime.lab's [Upberry](https://www.instagram.com/p/CPGakesLwBo/){:target="_blank"}.

Neither of these boards are exactly what I was looking for for my use case. So iI was motivated to design my own, customized board to my specs and that's how the Less-is-More board series came to be. 

I forked this board design from [Shawn Hymel](https://github.com/ShawnHymel/rpi-cm4-base-carrier){:target="_blank"}. He has a two part youtube series where he goes through how to design a CM4 Carrier Board.

- [Part 1 - How to Make a Raspberry Pi Compute Module 4 Carrier Board in KiCad](https://www.youtube.com/watch?v=ypcPJC_umPQ){:target="_blank"}
- [Part 2 - How to Make a Raspberry Pi Compute Module 4 Carrier Board in KiCad](https://www.youtube.com/watch?v=ge6gYIENo8Q&t){:target="_blank"}

Feel free to modify this design for your own application. 

Here is the current sepc and feature list of designed and planned carrier boards:

Model | Power (5V USB-C) | LEDs (Power/Activity) | Flashing Capability | SD card slot | Development Stage
:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:
LiM Board | &#10004; | &#10004; | &#10060; | &#10060; | [prototype](pages/LiM_Board.html)
LiM+ Board | &#10004; | &#10004; | &#10004; | &#10060; | [prototype](pages/LiM+_Board.html)
LiM++ Board | &#10004; | &#10004; | &#10004; | &#10004; | abandoned

While working onthe LiM++ Board I, got interested in clustering and decided to switch directions. I wanted to add a ccouple more features that coule be of value for a cluster type carrier board. And so the LiM CM4 Cluster Carrier Board idea was born. The LiM CM4 Cluster Board has the following features:

{::nomarkdown}<ul><li>RPi Pi Compute Module 4 (CM4) support (both versions with and w/o eMMC - Lite version)</li><li>PoE Gigabit RJ45 port (GbE)</li><li>USB-C (USB 2.0) for CM4 module flashing (power + data)</li><li>toggle switch for boot/flash mode</li><li>microSD card slot</li><li>2232 and 2241 M.2 M-key socket dedicated for NVMe PCIe SSD drives</li><li>Fan Pins</li><li>SDA, SDC, YCC, GND pins for OLED Displays</li></ul>{:/} 

This is a summary of my Journey 1 and how I designed and built a custom carrier board for the cluster idea I

# Journey 4 - The (LiM) Raspberry Pi cluster (Pringles) tray

{% assign myOtherPost = site.posts | where:"url", "/2022/12/25/the-lim-raspberry-pi-cluster-tray/" | first %}

{{ myOtherPost.content }}


## Here is the third option

<script>

 Document doc = Jsoup.connect("/2022/12/25/the-lim-raspberry-pi-cluster-tray/index.html").get();
         Elements links = doc.select("link");
         Elements scripts = doc.select("script");
        for (Element element : links) {
              System.out.println(element.absUrl("href"));
        }
        for (Element element : scripts) {
              System.out.println(element.absUrl("src"));
        }
</script>
