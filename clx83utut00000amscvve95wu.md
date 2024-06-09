---
title: "Homelab Introduction"
datePublished: Sun Jun 09 2024 22:17:49 GMT+0000 (Coordinated Universal Time)
cuid: clx83utut00000amscvve95wu
slug: homelab-introduction
canonical: http://chetenright.com/content/dist/blog/01-homelab-introduction/
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1717971764152/205ea5d6-b1e8-4d74-b206-67a59ad69190.jpeg
tags: homelab

---

## **Introduction**

I recently grew tired of occupying my personal gaming station with long running machine learning experiments. Also, a neighbor (broke college kid) was interested in ML and didn't have the money for a nice NVIDIA - so he needed to access the box as well. From that starting point, our homelab was born.

![](https://docs.google.com/drawings/d/e/2PACX-1vT2OWW8xhod1r1Ra6RuhIYGDuDgl8BlkUrJqOcxuiUNiNrKrtwrYgZVgpVjfNB0xkUSe35-4C-yDLoW/pub?w=720&h=520 align="left")

### **Hardware**

It's not going to break any records, but it gets the job done - and it was free!

| Part | Type | Description |
| --- | --- | --- |
| CPU | Intel i7-9700K | 8 core, 3.60 GHz |
| RAM | 48 Gb | DDR4 |
| GPU | NVIDIA GTX 2070 | 8 Gb |
| HDs | 7.25 TB total | 250 Gb NVMe |
|  |  | 2 TB SSD |
|  |  | 5 TB normal HDs |
|  |  |  |

### **OS**

So this was an interesting choice. Because I wanted to do Machine Learning work, and I wanted to use CUDA, there were issues. NVIDIA drivers do not allow virtualization, since they have a licensed product that provides that. I could not get Proxmox or any of the other hypervisors to play nicely - I use ESXI at work, so I wanted to do something different. In the end, I wound up doing a simple Ubuntu 19.04 installation and then put KVM and Docker (plus NVIDIA's dockers) on top. More on that below.

### **Virtualization**

Since I still wanted to have the ability to spin up true VMs and not just Dockers, I had to get a bit creative.

After installing KVM proper, I wasn't happy having to SSH in to do anything - I wanted a fancy web interface (like Proxomox, Xen frontends, etc.). I stumbled upon a Docker called **webvirtmgr**, which worked pretty well out of the box (I'm still connecting to it over SSH tunnel, but I only have to start up the tunnel. Eventually, I'll have it just running behind nginx + SSL).

Webvirtmgr worked pretty well out of the box, but I ran into issues with using KVM's default VNC. What I ended up doing was port forwarding *within the docker* using the wonderful **socat**. I think it ended up being more complicated than normal since I was tunneling over SSH (since webvirtmgr is http by default and unencrypted VNC).

### **Services**

Obviously I started this server wanting to do some Machine Learning, so there is a Jupyter notebook docker running with [Fast.ai](http://Fast.ai). Surprisingly, a few other needs came up as well. There will probably be some blog posts on these in the future - stay tuned!

* Windows VM for some robot programming testing (didn't want to remote into work)
    
* MacOS VM to learn Swift and app programming (all real compilation will be done on my MacBook to stay kosher with the Apple ToS)
    
* My favorite - a small Linux VM (with X) to automatically add entries from my Youtube playlist into my Google Music (always support content creators and only use such a method with NCS or other properly licensed content).
    

### **Future**

That's about it for the current state of the homelab - any questions or comments about anything, hit me up on twitter where I'm @amunchet. Until next time!