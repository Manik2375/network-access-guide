
# Background

So my university has a SMB server in their local network, it contains all  the lecture notes, slides and study stuff. Problem is, I want to access it not only in local network but everywhere (yes everywhere). 
That's the backstory.

# Plan

We need a computer running 24/7, which is connected to university network and to which we can connect from the outside and access the data (Raspberry pi :D).

Hmm, Port forwarding is probably the easiest, but I don't have access to the router. and I can't ask for access. 
(Well if you can get access, like in home router, you need to get an static ip first for better access)

My initial plan was to at least forward ssh port to access my computer,

So there are 3 ways, that comes into mind:

**1) Use tailscale (or Zerotier):** 
Probably the easiest way that I'd like to recommend you to try first. It gives you a unique IP without needing to talk to bad ISP. Easily forward ports and access the network. 
But for me, tailscale was blocked by network (yeah), so I tried Zerotier with ssh first. It worked... kind of. First it was very slow for me (because it couldn't do P2P, because of firewalls). Initially I was just trying to use ssh, but it only connected, I could never write anything. (Probably CGNAT and stuff) (If it's your first time doing this, dont worry, I will try explaining them later or just google it)
So yeah they didn't work

**2) Use ngrok or cloudflare tunnel:**
  cloudlfare is kinda blocked everywhere in university, I tried cloudflare warp for 4 days until it stopped working anymore, so I was bit uneasy spending my time with this (Also you need to get a domain for stable access) . ngrok required paid plan for tunneling ssh so not that too. 

**3) Use a custom VPS (Virtual Private server):**
The benefit of this approach is, I have a lot of flexibility. I can always host a webserver, which can keep running 24/7 even when my computer at university is turned off (no network or some other failure)

And it was the best option I could think of (And fun too)

# Prerequisite
1) A computer that can run while being connected to university network - Raspberry pi 
2) A VPS: You can use vps from any provider like Google, AWS, DigitalOcean or Azure - I will be using Azure
3) A domain (Optional if you have static ip)


# Let's start :D

Cool, i believe you have everything. Now
1) Start the raspbery pi and another device (mobile or computer), and make sure that they are in same network.

Now, we will set up ssh in Raspberry pi. (https://www.raspberrypi.com/documentation/computers/remote-access.html). Now connect your other device to the raspbery pi ssh (Although we won't be using ssh in final setup, I thought it might be a good start) 
> (**Note:** to connect to your raspberry pi in android, you can use `termux` or `termius` from play store)

Make sure everything's working. 

2) Now time for your vps. If you are using Azure, any B version low powered vps will suffice. First try connecting to your vps using ssh (default port is 22). Once your VPS is started, the process of connecting is similar to that of raspbery pi. Just make sure that your network rules are working. 

> **NOTE:** If for some reason you aren't able to connect to your virtual machine, try connecting using a different network. If it works on different network, it must mean that your network (like my university) is blocking `22` port. To fix that, we need to move our ssh port to somewhere else.
>
> The best option is `443` port which is for HTTPS connection (you should try other ports before this). To change your ssh port, use `sudo vim /etc/ssh/sshd_default` (or you can use any other text editor). And there find `Port 22` and change it to `Port 443`.
>
> Then find `GatewayPorts no` and change it to `GatewayPorts yes` (we will explain this later)
>
> Then run `sudo systemctl restart sshd` (But for me port change didn't work until I restarted the whole vps) 


Cool, now your vps has a ssh port that works with your local network. 

Now vps stuff is mostly done, now let's move to raspberry pi. 

Our plan is to connect to the smb server, mount it to the filesystem and access that mounted files everywhere :D

We need some packages: 
1) autossh - To make our ssh connection consistent between raspbery pi and vps, like if connection gets closed, autossh will try to reconnect
2) FileBrowser - To create a good looking interface for viewing files (https://filebrowser.org/)[https://filebrowser.org/)
> **NOTE:** Technically, if we just forward our ssh through vps to internet, we can directly view our mounted filesystem thanks to SFTP, but we need a SFTP client for that, like `termius` provides that, but I feel that's bothersome
3) Install packages for command yourself that don't work.

After everything's installed, we can easily mount the smb server to our filesystem.

First, let's create a directory,
```bash
mkdir -p /mnt/smbshare
```

Now, let's connect to smb server and mount it to the created directory
```bash
sudo mount -t cifs //smbserverIP/sharename /mnt/smbshare -o username=pi,password=yourpass
```

Cool, if you have successfully mounted a smbserver you can confirm by doing 
```bash
ls /mnt/smbshare
```

After confirmed, let's start the filebrowser. The default port is `8080`
```bash
filebrowser -R /mnt/smbshare
```

Now you can view your smbserver files for selected sharename in `localhost:8080` in browser. Hurray!!
