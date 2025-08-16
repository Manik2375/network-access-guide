
# Background

So my university has a SMB server in their local network, it contains all  the lecture notes, slides and study stuff. Problem is, I want to access it not only in local network but everywhere (yes everywhere). 
That's the backstory.

# Plan

We need a computer running 24/7, which is connected to university network and to which we can connect from the outside and access the data (Raspberry pi :D).

Hmm, Port forwarding is probably the easiest, but I don't have access to the router. and I can't ask for access. 
(Well if you can get access, like in home router, you need to get an static ip first for better access)

My initial plan was to at least forward ssh port to access my computer,

So there are 3 ways, that comes into mind:

1) Use tailscale (or Zerotier): 
Probably the easiest way that I'd like to recommend you to try first. It gives you a unique IP without needing to talk to bad ISP. Easily forward ports and access the network. 
But for me, tailscale was blocked by network (yeah), so I tried Zerotier with ssh first. It worked... kind of. First it was very slow for me (because it couldn't do P2P, because of firewalls). Initially I was just trying to use ssh, but it only connected, I could never write anything. (Probably CGNAT and stuff) (If it's your first time doing this, dont worry, I will try explaining them later or just google it)

So yeah they didn't work

2) Use ngrok or cloudflare tunnel:
  cloudlfare is kinda blocked everywhere in university, I tried cloudflare warp for 4 days until it stopped working anymore, so I was bit uneasy spending my time with this (Also you need to get a domain for stable access) . ngrok required paid plan for tunneling ssh so not that too. 

3) Use a custom VPS (Virtual Private server) 
The benefit of this approach is, I have a lot of flexibility. I can always host a webserver, which can keep running 24/7 even when my computer at university is turned off (no network or some other failure)

And it was the best option I could think of (And fun too)

# Prerequisite
1) A computer that can run while being connected to university network - Raspberry pi 
2) A VPS: You can use vps from any provider like Google, AWS, DigitalOcean or Azure - I will be using Azure
3) A domain (Optional if you have static ip)


