# 3 & 4 - Putting it all together - Building the cluster
After what felt like an eternity (2+ years) I had all the components together to finally build my LIM cluster.

## Hardware Build
1. The first step was to assemble the eight cluster nodes.

![limcluster_build1](/images/limcluster_build/limcluster1.png)

In the above picture, a depiction of all the parts making up a cluster node:
1.	LiM CM4 Cluster carrier board
2.	Raspberry Pi Compute Module (CM) 4 Light 8GB RAM with WiFi - CM4108000
3.	128x32 i2c OLED display
4.	128GB 2232 M.2 M-key socket NVMe PCIe SSD drive
5.	CM4 Heat sink with thermal compound strip and screws
6.	Customer designed 90 degree i2c OLED angle bracket
7.	Brush less LED cooling fan CPU with screws
8.	Not pictured – M2.5 brass standoffs with screw

Assembly line, the parts to put all eight together:

![limcluster_build2](/images/limcluster_build/limcluster2.png)

And this is now how eight of the cluster nodes look like put together:

![limcluster_build3](/images/limcluster_build/limcluster3.png)

Assembled cluster with eight cluster nodes in the Pi (Pringles) Tray standing on top of a 8 port PoE router:

![limcluster_build4](/images/limcluster_build/limcluster4.png)

Cluster top view:

![limcluster_build5](/images/limcluster_build/limcluster5.png)

## Software Build
Since I wanted to use Rancher to manage the cluster, I also prepared an [Intel based UP 4000 Board](https://up-board.org/up-4000/){:target="_blank"} and installed Ubuntu 22.04 and Rancher 2.5 on it (more on the Rancher install further down). I set this up as the ‘clustercontroller’ – 10.0.0.200. All the management of the cluster (Rancher, Ansible) is done from the clustercontroller node.

So, for the most part to install the cluster software I followed NetworkChuck’s video tutorial ["i built a Raspberry Pi SUPER COMPUTER!! // ft. Kubernetes (k3s cluster w/ Rancher)"](https://www.youtube.com/watch?v=X9fSMGkjtug). I wanted to automate the cluster build as much as I could, so where possible I created Ansible scripts. But first, I needed to install [Raspberry Pi OS](https://www.raspberrypi.com/software/){:target="_blank"} on the cluster nodes.

{:start="1"}
1. Imaging
Since the cluster nodes are built with SSD drives, I used the Raspberry Pi Imager to write the OS to the eight SSD drives. 

![limcluster_build6](/images/limcluster_build/limcluster6.png)

Initial set up/configuration: 

-	Enabled SSH
-	I did not enable wireless as all nodes are using network cables connected to a PoE router which also power the nodes.  
-	I did not add “arm_64bit=1” to the config.txt file as I was already installing Raspberry Pi OS Bullseye 64-bit.
-	All I changed in between imaging the next node was the hostname.
-	From my test cluster set up I configured my ubiquity Dream machine to assign a static IP to each of the cluster nodes based on MAC address so I could easily identify the cluster nodes on my network. The IP address and hostname schema looks as follows:
	* Cluster node 1: 10.0.0.201 – clusternode1
	* Cluster node 2: 10.0.0.202 – clusternode2
	* …
	* Cluster node8: 10.0.0.208 – clusternode8

{:start="2"}
2. Once I booted up all eight cluster nodes I copied ssh-keys across so I can communicate and administer the nodes from the clustercontroller. I created the following ansible hosts file in /etc/ansible/:

```bash
[clustercontroller]
10.0.0.200

[clustermaster]
10.0.0.201

[clusterworkers]
10.0.0.202
10.0.0.203
10.0.0.204
10.0.0.205
10.0.0.206
10.0.0.207
10.0.0.208

[limcluster]
10.0.0.201
10.0.0.202
10.0.0.203
10.0.0.204
10.0.0.205
10.0.0.206
10.0.0.207
10.0.0.208

[testnode]
10.0.0.209
```

Then I tested it by pinging the cluster with Ansible:

![limcluster_build7](/images/limcluster_build/limcluster_ping.gif)

{:start="3"}
3. Next, I updated the OS on all cluster nodes with the following Ansible script:

```yaml
---
- hosts: "{{ "{{" }} variable_hosts {{ }} }}"
  remote_user: pi
  become: true
  become_user: root
  gather_facts: False

  tasks:
    - name: Update apt repo and cache on all Debian/Ubuntu boxes
      apt:
        update_cache: yes
        force_apt_get: yes
        cache_valid_time: 3600

    - name: Upgrade all packages on servers
      apt:
        upgrade: dist
        force_apt_get: yes

    - name: Check if a reboot is needed on all servers
      register: reboot_required_file
      stat:
        path: /var/run/reboot-required
        get_md5: no

    - name: Reboot the server if kernel updated
      reboot:
        msg: "Reboot initiated by Ansible for kernel updates"
        connect_timeout: 5
        reboot_timeout: 300
        pre_reboot_delay: 0
        post_reboot_delay: 30
        test_command: uptime
      when: reboot_required_file.stat.exists

```

![limcluster_build8](/images/limcluster_build/limcluster_upgrade.gif)

{:start="4"}
4. To add ```“cgroup_memory=1 cgroup_enable=memory”``` I wrote the following Ansible script so I can easily do this across multiple cluster nodes and also easily add additional nodes if wanted/needed:

```yaml
---
- hosts: "{{ "{{" }} variable_hosts {{ }} }}"
  remote_user: pi
  become: true
  become_user: root
  tasks:
    - name: Check whether /boot/cmdline.txt contains 'cgroup_memory' and append recommended cluster node vars if not found
      command: "grep  'cgroup_memory' /boot/cmdline.txt"
      register: checkmyconf
      check_mode: no
      ignore_errors: yes
      changed_when: no
      failed_when: false
    - meta: end_host
      when: checkmyconf.rc == 0

    - name: Add cgroup_memory to /boot/cmdline.txt
      ansible.builtin.lineinfile:
        path: "/boot/cmdline.txt"
        backrefs: true
        regexp: '^(.*rootwait.*)$'
        line: '\1 cgroup_memory=1 cgroup_enable=memory'
      register: updated
      when: checkmyconf.rc != 0

    - name: Reboot when /boot/cmdline.txt was updated with recommended cluster node vars
      reboot:
        connect_timeout: 5
        reboot_timeout: 300
        pre_reboot_delay: 0
        post_reboot_delay: 30
        test_command: uptime
      when: updated.failed == false
```
![limcluster_build9](/images/limcluster_build/limcluster_append_cmdline.gif)

{:start="5"}
5. I DID NOT follow 'Step 2 – K3s Prep' of NetworkChuck’s tutorial and skipped the configuration of legacy IP tables. I continued NetworkChuck’s tutorial with the installation of the masternode of the k3s cluster. I created the following Ansible script for the installation of the masternode:

```yaml
---
- hosts: "{{ "{{" }} variable_hosts {{ }} }}"
  remote_user: pi
  become: true
  become_user: root
  tasks:
    - name: Check if k3s is already installed
      register: k3s_installed
      stat: path=/usr/local/bin/k3s get_md5=no

    - name: k3s is already installed and exit
      debug:
        msg: "k3s is already installed on {{ "{{" }} ansible_hostname }}"
      when: k3s_installed.stat.exists

    - meta: end_host
      when: k3s_installed.stat.exists

    - name: Install k3s on master node remote target
      shell: curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="v1.21.1+k3s1" K3S_KUBECONFIG_MODE="644" sh -s –
```

![limcluster_build10](/images/limcluster_build/limcluster_k3s_masternode_install.gif)

{:start="6"}
6. Next, on to installing the cluster nodes with this Ansible script:

```yaml
- hosts: "{{ "{{" }} variable_master {{ }} }}"
  gather_facts: false
  user: pi
  become: true
  become_user: root
  tasks:
     - name: "Read k3s cluster master token"
       shell: |
         cat /var/lib/rancher/k3s/server/node-token
       register: file_content
     - name: "Add k3s cluster master token to dummy host"
       add_host:
         name: "master_token_holder"
         hash: "{{ "{{" }} file_content.stdout }}"
         ip: "{{ "{{" }} inventory_hostname }}"

- hosts: "{{ "{{" }} variable_worker {{ }} }}"
  user: pi
  tasks:
    - name: Check if k3s is already installed
      register: k3s_installed
      stat: path=/usr/local/bin/k3s get_md5=no

    - name: k3s is already installed and exit
      debug:
        msg: "k3s is already installed on ""{{ "{{" }} ansible_hostname }}"
      when: k3s_installed.stat.exists

    - meta: end_host
      when: k3s_installed.stat.exists

    - name: Install k3s worker on remote target
      shell: curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="v1.21.1+k3s1" K3S_KUBECONFIG_MODE="644" K3S_TOKEN="{{ "{{" }} hostvars['master_token_holder']['hash'] }}" K3S_URL="https://"{{ "{{" }} hostvars['master_token_holder']['ip'] }}:6443" K3S_NODE_NAME="{{ "{{" }} ansible_hostname }}" sh -

```
![limcluster_build11](/images/limcluster_build/limcluster_k3s_workernodes_install.gif)

{:start="7"}
7. After I had the cluster installed and running, I followed NetworkChuck's instructions to install Rancher 2.5 on my clustermaster

![limcluster_build12](/images/limcluster_build/rancher.png)

{:start="8"}
8. Once Rancher was installed, I connected the limcluster to it

![limcluster_build13](/images/limcluster_build/rancher_complete.gif)

![limcluster_build14](/images/limcluster_build/rancher_screenshot.png)

![limcluster_build15](/images/limcluster_build/rancher_screenshot1.png)

{:start="9"}
9. Now that the limcluster was set up, I followed NetworkChuck's demos and first installed a couple of instances of nginx

![limcluster_build16](/images/limcluster_build/nginx.png)

{:start="10"}
10. After that, I tried the load balancing demo

![limcluster_build17](/images/limcluster_build/rancher_cows.gif)

{:start="11"}
11. I also did get the Minecraft server install working via Rancher GUI, exactly as descriped by NetworkChuck in his video.

![limcluster_build18](/images/limcluster_build/limcluster-minecraft.png)
Minecraft deployment in the Rancher GUI.

![limcluster_build19](/images/limcluster_build/limcluster-minecraft.gif)
Minecraft Java edition connecting to the minecraft server running on the limcluster.

**Note:** I did run into an issue that caused the minecraft deployment to 'crashloop' and constantly reboot. The issue is described [here:](https://stackoverflow.com/questions/58331710/launching-helm-chart-stable-minecraft-on-kubernetes-1-14-on-eks-fails-liveness-p){:target="_blank"}

I fixed it by changing the following values in the helm chart for the minecraft deployment:
```yaml
...
livenessProbe:
...
   initialDelaySeconds: 90
...
readinessProbe:
...
   initialDelaySeconds: 30
...
```

{:start="12"}
12. The last step for me was to get the OLEDs working to display the stats of each node.

This was more of a fun add on that is not really necessary for the functionality of the cluster but I thought it would be nice to display basic system information on a small OLED display for easier identification of the indidual nodes.

See my seperate page on this side project for now: [oleddisplaystats](https://oleddisplaystats.loonix.ca/){:target="_blank"} and the more Raspberry Pi/limcluster specific: [displaypistats](https://displaypistats.loonix.ca/){:target="_blank"}

