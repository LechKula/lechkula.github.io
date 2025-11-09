---
layout: post
title:  "NAS with Raspberry Pi"
date:   2025-11-05 15:38:00 +0100
categories: jekyll update
---
I dual boot Ubuntu (Linux) and Microsoft Windows. When I started at Chalmers, I actually only ran Ubuntu because it was a hobby I had picked up the summer before. But during my years at Chalmers I started noticing that some things, mainly MATLAB, works better on Windows. Therefore I decided to also install Windows. It works quite well, except that sometimes I might work on the same project on both Ubuntu and Windows. Sharing files then becomes a hassle and I prefer not being dependent on an external cloud provider. I therefore decided to build my own NAS with a Raspberry Pi and an external hard drive. The full list of components is

- Raspberry Pi 5 Starter kit (comes with a convenient package to safeguard the Raspberry Pi, which also has a fan to keep it cool and other useful cables)
- Kingston XS1000 SSD (1 TB)
- NÖRDIC Gen2 3.2 USB-A 4ports Powered Hub (since the power supply from just the Raspberry Pi might be too weak.)
- Ethernet cable
- Connecting cables

I followed the guide at [How to build a Raspberry Pi NAS](https://www.raspberrypi.com/tutorials/nas-box-raspberry-pi-tutorial/ "NAS box Raspberry Pi tutorial"). Following the guide was quite straight forward, even though some unforseen struggles apeared. A stupid problem I had was that I did not have a monitor to connect the Raspberry Pi to for initial set-up. After borrowing a monitor and setting up remote access with TigerVNC I was good to go and follow the guide. Another problem that appeared was that the Kingston SSD automatically mounted under /media. But unmouting is as simple as 
{% highlight bash %}
umount sda1
{% endhighlight %} after which the drive could be formatted and mounted in accordance with the guide. I now have a NAS with which I can share files between my Linux and Windows systems. Here is a screenshot from my Ubuntu system, with a 'Hi!' to my Windows system 
![hi from ubuntu](/assets/hi_from_ubuntu.png) 

and the same files on my Windows system, with a 'Hi!' back to the Ubuntu system!
![hi from windows](/assets/hi_from_windows_screenshot.png) 
I can even access the files on my smart phone! 
![hi from iphone](/assets/hi_from_iphone.jpeg) 
