---
title: Enhancing WSL Connectivity with VPN Integration (Cisco, Kaspersky, others)
date: 2024-02-20 22:15:11
tags:
  - WSL
  - VPN
  - Cisco
  - Kaspersky
  - Windows
  - Linux
  - Networking
---

## Introduction

Windows Subsystem for Linux (WSL) provides a seamless integration of Linux with Windows, allowing users to run a Linux distribution alongside their Windows environment. However, when using a VPN, configuring WSL to work seamlessly with it can be a bit tricky. In this guide, we will walk you through the steps to ensure that your WSL setup remains connected and functional while your VPN is active.

<!--more-->

## Step 1: Edit WSL Configuration

The first step is to edit the WSL configuration file to execute a script when WSL starts. Open the WSL configuration file using your preferred text editor (in this case, we use vim):

`vim /etc/wsl.conf`

Add the following lines to the file:

```
[boot]
command=sudo /bin/vpn-dns.sh

[network]
generateResolvConf = false
```

Save and exit the editor.

## Step 2: Create the Script

Now, create the script that will be executed during WSL startup. Create a new file using vim or any text editor of your choice:

`vim /bin/vpn-dns.sh`

Paste the following script into the file:

```bash
#!/bin/bash

echo "Getting current DNS servers, this takes a couple of seconds"

/mnt/c/Windows/System32/WindowsPowerShell/v1.0/powershell.exe -Command '
$ErrorActionPreference="SilentlyContinue"
Get-NetAdapter -InterfaceDescription "Kaspersky VPN*" | Get-DnsClientServerAddress | Select -ExpandProperty ServerAddresses
Get-NetAdapter | ?{-not ($_.InterfaceDescription -like "Kaspersky VPN*") } | Get-DnsClientServerAddress | Select -ExpandProperty ServerAddresses
' | \
        awk 'BEGIN { print "# Generated by vpn fix func on", strftime("%c"); print } { print "nameserver", $1 }' | \
        tr -d '\r' > /etc/resolv.conf
clear
```

Save and exit the editor. Make the script executable:

```bash
sudo chmod +x /bin/vpn-dns.sh
echo "$(whoami) ALL=(ALL) NOPASSWD: /bin/vpn-dns.sh" | sudo tee /etc/sudoers.d/010-$(whoami)-vpn-dns
```

## Step 3: Restart WSL

To apply the changes, restart WSL using the following command in either Command Prompt or PowerShell:

`wsl --shutdown`

## Conclusion

By following these steps, you have successfully configured WSL to work seamlessly with your VPN. This ensures that your DNS settings are appropriately adjusted, allowing WSL to maintain connectivity even when your VPN is active. Enjoy a hassle-free experience with WSL in a VPN-enabled environment!

References: [Stackoverflow](https://superuser.com/a/1666368)
