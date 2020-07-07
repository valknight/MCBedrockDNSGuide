# Minecraft: Bedrock Edition Custom Server DNS Trick 
  * [About](#about)
    + [What is this?](#what-is-this-)
    + [What platforms/versions have been reported working?](#what-platforms-versions-have-been-reported-working-)
    + [How does this work?](#how-does-this-work-)
    + [Why would I want to do this?](#why-would-i-want-to-do-this-)
    + [Limitations of this trick](#limitations-of-this-trick)
    + [List of the domains for featured servers](#domains-list)
  * [Setup](#setup)
    + [Requirements](#requirements)
    + [DNS Setup on the server](#dns-setup-on-the-server)
    + [DNS setup on the console](#dns-setup-on-the-console)
  * [Making Improvements](#making-improvements)

## About

### What is this?

This is a guide to use your own servers on platforms that run Minecraft (Bedrock Edition) that do not have this ability built into the game.

### What platforms/versions have been reported working?

- Minecraft Bedrock Edition v1.11.4 for Xbox One
- Minecraft Bedrock Edition v1.16.1 for Nintendo Switch

This guide may also work on other platforms which run Minecraft Bedrock Edition in future, and new versions of the game, however support isn't guaranteed. If it is working on platforms not listed above, please create an Issue saying as such, and I can update the list above.

This **will not** work on any deprecated version of Minecraft (such as Minecraft for Nintendo Switch or the old PS4 version).

### How does this work?

Mojang added to the game an option on the Play menu labelled "Featured Servers" - these are servers approved and deemed as "safe" by the team at Mojang, and so, are allowed on platforms where it is deemed custom servers aren't allowed. The domain names for these Featured Servers however are fixed (for example, "Lifeboat Network" has the fixed address of mco.lbsg.net). As such, by using a custom DNS server, we can report mco.lbsg.net to be located at the IP of your custom server.

### Why would I want to do this?

One, this allows for cross-platform play without one host being always online without using Minecraft Realms. Second, it also allows for custom server software to be used (such as PocketMine-MP), allowing for you to add extra features to your multiplayer sessions. Third, in my own experience, performance of the game on the Nintendo Switch is generally better.

### Limitations of this trick

- You will no longer be able to connect to one of the featured servers, without changing your DNS back in your device's settings
    - This is fairly easy to do, but should be kept in mind
- This may be patched at any point by the Minecraft team (however, it has not happened yet, and this trick has been around for several versions of the game)
- If your DNS server goes down, all consoles using it will lose network connectivity (setup two if this is an issue for you, so you can take one down without the other being affected)

### Domains List
-The featured servers in bedrock do not use the standard domains, so I compiled a list of them here. 

- The Hive uses hivebedrock.network
- Lifeboat uses mco.lbsg.net
- mineplex uses mco.mineplex.com
- cubecraft uses mco.cubecraft.net
- mineville uses play.inpvp.net


## Setup

### Requirements

- A Bedrock Compatible Minecraft Server
    - As mentioned, this can be any server software you wish, however, it has been primarily tested with the official server software that Mojang releases
    - This needs to be set to use port 19132 (the default port) and should be open to the internet
    - Whitelists are recommended to stop trolls, however, it's up to you
- A server which you can reach DNS from
    - This can be locally on your network, but, if you want friends to be able to use it, you likely want to have port 53 avaliable
    - To make this easier, I use a droplet I had from DigitalOcean lying around, and installed the DNS server there, so I didn't need to open my home server's DNS up
- A domain name is recommended - if you don't have one, a dynamic address from NoIP or similar provider is fine - this is just to ensure that if your IP does change, your server doesn't stop working

This guide assumes your server is already working. To test this, you can use Minecraft for Windows 10, or Minecraft on mobile devices (for those of you on Linux, check out the project MCPELauncher).

### DNS Setup on the server

> This guide assumes you're running Linux, however, I've tried to make it easier to follow along on Windows

1. Download CoreDNS
    - In case the config spec changes in future, this guide uses version 1.5.0
2. Extract it with `tar -xzf coredns_1.5.0_linux_amd64.tgz` (Windows users: use 7zip to extract this archive)
3. Create a file named `Corefile` (no extension) in the same folder as the Coredns executable.
4. Open it, and copy the following contents in

```
.:53 {
    rewrite name exact mco.lbsg.net example.com
    forward . 1.1.1.1:53
}
```
While doing this, replace example.com with the address for your server (this cannot be a raw IP address).

If you want to use a DNS server other than Cloudflare's for any other requests, change `1.1.1.1`

> This file will tell CoreDNS to treat all requests to mco.lbsg.net (the Lifeboat Network) as requests instead for `example.com`, which is the core component of the trick

5. (Skip this step if you use Windows) Mark CoreDNS as executable using `chmod +x ./CoreDNS`

6. Run CoreDNS
    - On Windows, you can run CoreDNS.exe from command prompt
    - On Linux, you will need to use root permissions to run it, as Linux prevents non-root processes from communicating on port 53 - do this with `sudo ./CoreDNS`

### DNS setup on the console

> This assumes for now you are using a Nintendo Switch. The process will be similar for other consoles, apart from the Switch specific menus

1. Ensure Minecraft is fully closed (not just suspended)
2. Head to Settings > Internet > Internet Settings
3. Tap your preferred Internet connection, then tap "Change Settings"
4. Scroll down to "DNS Settings" and change this from "Automatic" to "Manual"
5. In Primary **and** Secondary DNS, enter the IP address of your DNS server
    - If you aren't sure of the IP of your server, there are multiple websites on the internet to find this
    - If you are on a cloud server (e.g a DigitalOcean Droplet/Linode Instance) check your providers web panel for this (you should already know it though)
    - For stability you should repeat the DNS server instructions on two different servers, on unique networks, so if one becomes unavaliable, you don't lose network connectivity on your console
6. Tap "Save", then "Connect to This Network"
7. Go to the home screen, and open Minecraft
8. Go to "Play" then navigate across to "Servers" - the Lifeboat network should now show your MOTD
9. Connect to the "Lifeboat Network" - you should now connect to your server instead

## Making Improvements

If you have improvements/fixes (including typos/grammar - mine is likely appalling!) you wish to add to this guide, please make a pull request.

If you want to report specific compatibility with platforms/versions not listed, please make an Issue, and report the platform, version of the game, and how well this trick works, and the guide can be updated with that information.

## Further reading

If you want to play using a Java server, check out [Geyser](https://github.com/GeyserMC/Geyser). It works well, and lets me play on Java servers while curled up in bed.

If you want to have a server browser on Switch, check out [BedrockConnect](https://github.com/Pugmatt/BedrockConnect). It's great as it means at most you take out one of the built in servers, and lets you have many many more saved. It's also light enough to be ran on a Raspberry Pi without issues.
