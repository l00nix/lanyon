---
title: "(LiM) Kubernetes Cluster set up using Ansible"
layout: post
published: false
---

Inspired by “Network Chuck”’s video tutorial[ “i built a Raspberry Pi SUPER COMPUTER!! // ft.
Kubernetes (k3s cluster w/ Rancher)” on youtube](https://www.youtube.com/watch?v=X9fSMGkjtug){:target="_blank"} and corresponding course on his [website](
https://learn.networkchuck.com/courses/take/raspberry-pi-projects/lessons/29763478-i-built-a-
raspberry-pi-super-computer-ft-kubernetes-k3s-cluster-w-rancher/discussions/index){:target="_blank"} as well as Jeff Geerling’s [video channel](https://www.youtube.com/@JeffGeerling){:target="_blank"} and [blog](https://www.jeffgeerling.com/blog){:target="_blank"}, I set out on three journeys:

1. The first led me down my father’s foot steps of electrical engineering designing and building a custom carrier board for the Raspberry Pi compute module.
2.  The second, learning and creating kubernetes clusters on Raspberry Pis with [kubernetes](https://kubernetes.io/){:target="_blank"}/[k3s](https://k3s.io/){:target="_blank"} - a light weight version of kubernetes and managing them with [Rancher](https://www.rancher.com/){:target="_blank"}. 
3.  The third, learning a new to me way of automating/simplifying administrative tasks, cluster installs and management using [Ansible](https://www.ansible.com/){:target="_blank"}.

I should add that I am well down the path of my career but I always enjoyd tech and Linux. However, I am doing all of this as a hobby.

# Journey 1 - Developing the LiM Cluster CM4 Carrier Board
Inspired by the Jeff Geerlings video [New Raspberry Pi Projects - CM4 NAS, Piunora, and Seaberry!](https://www.youtube.com/watch?v=7Li7Nh9V74I&t=300s){:target="_blank"} where he talks about [mebs_t's self designed NAS](https://github.com/mebs/CM4-NAS){:target="_blank"}, made me think I am interested in doing somehting like this too. At the time I was looking for a Raspberry Pi Compute Module 4 (CM4) carrier board that only provides power to the CM4. Many of the carrier boards out at the time provided a lot more functionality so set out to design and build the [Less-is-More (LiM) carrier board](https://lim.loonix.ca/pages/LiM_Board.html){:target="_blank"}.. 
 – I have built
several clusters
