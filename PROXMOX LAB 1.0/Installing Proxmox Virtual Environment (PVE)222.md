> ⚠️ **Disclaimer:** This is a home lab environment built for learning and research purposes. None of these services should be exposed to the public internet. All IP addresses shown are examples — substitute your own addressing scheme when replicating this setup.

* * *

# Download Proxmox

Navigate to the [Proxmox website](https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso) and download the latest `.iso` file.  
<img src="../_resources/2d2b8704d5e682680687190091f9e599.png" alt="2d2b8704d5e682680687190091f9e599.png" width="1126" height="472" class="jop-noMdConv">

* * *

# Creating Bootable Media

There are three methods available depending on your operating system:

| Method | Best for |
| --- | --- |
| **USBimager** | Cross-platform (Windows, macOS, Linux) |
| **GNOME Disks** | Linux (GUI) |
| **`dd`** | Linux (terminal) |

## Method 1 — USBimager

Navigate to the [USBimager website](https://gitlab.com/bztsrc/usbimager) and download a compatible version for your working computer.  
<img src="../_resources/b99822c23934d2a799c85c0e9ef3dde2.png" alt="b99822c23934d2a799c85c0e9ef3dde2.png" width="1126" height="732" class="jop-noMdConv">  
Navigate to the extracted folder (e.g. `usbimager_1.0.10-x86_64-linux-x11/bin/`) and launch USBimager.  
<img src="../_resources/7c3b463680ca88cb9d822484305f4abb.png" alt="7c3b463680ca88cb9d822484305f4abb.png" width="1126" height="392" class="jop-noMdConv">

## Method 2 — GNOME Disks Utility

Insert the USB drive into the working computer and open GNOME Disks by searching for it in the applications menu.  
<img src="../_resources/57c9398ed66c4419312b3dead58fce67.png" alt="57c9398ed66c4419312b3dead58fce67.png" width="1126" height="616" class="jop-noMdConv">

Click on the flash drive in the list on the left to select it. Click the gear icon and select `Restore Partition Image` in the dropdown menu.  
<img src="../_resources/dead023eefad0cf0a8baa135ab4e7343.png" alt="dead023eefad0cf0a8baa135ab4e7343.png" width="1126" height="619" class="jop-noMdConv">  
<img src="../_resources/e03da9081bfdfc62a249433c3a8fe1eb.png" alt="e03da9081bfdfc62a249433c3a8fe1eb.png" width="1126" height="620" class="jop-noMdConv">

In the dialog box, choose the path to your Proxmox `.iso` file and click `Start Restoring`.  
<img src="../_resources/81212269ebef21776583cfa31fd8b7a5.png" alt="81212269ebef21776583cfa31fd8b7a5.png" width="1126" height="305" class="jop-noMdConv">

## Method 3 — `dd`

First identify the name of the USB drive using `lsblk`.  
<img src="../_resources/5d9c9a043cbf7764ed36259b4e15927a.png" alt="5d9c9a043cbf7764ed36259b4e15927a.png" width="1126" height="514" class="jop-noMdConv">

Ensure that the USB partition (e.g. `/dev/sda1`) is unmounted before proceeding. It is essential that we write to the entire disk, not just a single partition.  
<img src="../_resources/d4a982843e67cebef185539112ba1d67.png" alt="d4a982843e67cebef185539112ba1d67.png" width="1126" height="183" class="jop-noMdConv">

Then run the `dd` command, substituting the actual path to your downloaded ISO. The operation will take a few minutes depending on your USB drive speed. Once complete, it will return to the prompt.

```bash
sudo dd if=~/Downloads/proxmox-ve_9.1-1.iso of=/dev/sda bs=1M status=progress conv=fdatasync
```

> **Note:** Replace `~/Downloads/proxmox-ve_9.1-1.iso` with the actual path to your ISO file, and `/dev/sda` with the device name identified by `lsblk`. Double-check the target device — `dd` will overwrite it without confirmation.

<img src="../_resources/f97adfa1edd4c2dbf9f2be78188875c0.png" alt="f97adfa1edd4c2dbf9f2be78188875c0.png" width="1126" height="190" class="jop-noMdConv">

* * *

# Installing Proxmox on Hardware

Insert the bootable drive into the target hardware and power it on. In this case a Lenovo ThinkPad T470s is used as the server. During boot, press the BIOS menu key (**F12** on Lenovo) and select the bootable USB as the boot device.

> **Important:** Ensure the hardware is connected to the network via a wired Ethernet connection before proceeding. Wireless interfaces cannot be bridged in Linux without additional configuration and should not be selected during the Proxmox installer.

<img src="../_resources/77ed9d8544cd994dadff2ffeff8ab95b.png" alt="77ed9d8544cd994dadff2ffeff8ab95b.png" width="1126" height="601" class="jop-noMdConv">

Once the initial boot screen appears, select `Install Proxmox VE (Graphical)`.  
<img src="../_resources/32d9bab8671261c467a4c1792de69956.png" alt="32d9bab8671261c467a4c1792de69956.png" width="1126" height="634" class="jop-noMdConv">

The installation will proceed and, if successful, you will be presented with the **End User License Agreement (EULA)** page. Click `I Agree` to proceed with the initial setup.  
<img src="../_resources/e65cc664425ce246de0ebbc399fe64fe.png" alt="e65cc664425ce246de0ebbc399fe64fe.png" width="1126" height="703" class="jop-noMdConv">

Select the appropriate timezone, as Proxmox requires this for correct synchronization of services.  
<img src="../_resources/0aba8d95f306449b1996dfecfd544070.png" alt="0aba8d95f306449b1996dfecfd544070.png" width="1126" height="717" class="jop-noMdConv">

On the network configuration screen, select the **Ethernet NIC** as the management interface. Assign a Fully Qualified Domain Name (FQDN) for your local server. Set the **Gateway** to your router's IP address. Set the **DNS server** to your router's IP or a known public resolver such as `1.1.1.1`, unless you have a dedicated DNS server on the network. Assign the server a static IP address that is not already in use on your network.  
<img src="../_resources/5bf49c7b8a4dad625ce6d63a908ee3ee.png" alt="5bf49c7b8a4dad625ce6d63a908ee3ee.png" width="1126" height="782" class="jop-noMdConv">

Review the summary of your settings, click `Install`, and wait for the server to reboot.  
<img src="../_resources/a002d48af3e91d76d7cd5610cafde5f2.png" alt="a002d48af3e91d76d7cd5610cafde5f2.png" width="1126" height="821" class="jop-noMdConv">

* * *

## Troubleshooting: No Network Connectivity

If the server is unable to communicate with other devices or is not receiving internet connectivity, verify the network interface configuration:

```bash
nano /etc/network/interfaces
```

Ensure the correct interface is listed. If it is missing, add it manually. For example, `auto nic0` was absent in the configuration below and had to be added:

```
auto lo
iface lo inet loopback

auto nic0
iface nic0 inet manual

auto vmbr0
iface vmbr0 inet static
        address 192.168.x.x/24
        gateway 192.168.x.1
        bridge-ports nic0
        bridge-stp off
        bridge-fd 0
```

> **Note:** Replace `192.168.x.x` and `192.168.x.1` with your actual assigned IP and gateway.

Next, restart the networking service:

```bash
systemctl restart networking
```

Once the server is communicating over the network, access the Web GUI at `https://<your-server-ip>:8006` and sign in using the `root` account and the password set during installation.  
<img src="../_resources/a527cd753f7fa12eac0718cdabda3ec9.png" alt="a527cd753f7fa12eac0718cdabda3ec9.png" width="1126" height="475" class="jop-noMdConv">  
<img src="../_resources/b8115a535251a8f18b4f047ffbf8332d.png" alt="b8115a535251a8f18b4f047ffbf8332d.png" width="1126" height="582" class="jop-noMdConv">

> **Security note:** It is recommended to create a dedicated non-root admin user in Proxmox after initial setup. Logging in directly as `root` for day-to-day management is not best practice. The Web GUI should also be firewalled so it is only reachable from trusted management IPs.

* * *

# Updating the Server

Once logged in, navigate to the **Updates** section under your node. Click `Refresh`.

> **Why the repository error occurs:** By default, Proxmox is configured to use the Enterprise repository, which requires a paid subscription key. Since this is a home lab without a subscription, the enterprise repo will fail. You can safely switch to the no-subscription community repository by running the following in the node shell:
> 
> ```bash
> # Disable the enterprise repo
> echo "# disabled" > /etc/apt/sources.list.d/pve-enterprise.list
> 
> # Add the no-subscription community repo
> echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" \
>   > /etc/apt/sources.list.d/pve-no-subscription.list
> 
> apt-get update
> ```
> 
> After this, the repository error will no longer appear.

Once the packages table has been populated, click `Upgrade`.  
<img src="../_resources/ccdfd585c02ab5feda79195a2aca7c60.png" alt="ccdfd585c02ab5feda79195a2aca7c60.png" width="1126" height="612" class="jop-noMdConv">

A new terminal window will pop up. Type `Y` to confirm and accept the package upgrade. You can accept any defaults that appear during installation.  
<img src="../_resources/6ba057826c81b4d93b78c628e9eb80b3.png" alt="6ba057826c81b4d93b78c628e9eb80b3.png" width="1126" height="737" class="jop-noMdConv">

Once the update is complete, reboot the server for the updates to take full effect.  
<img src="../_resources/0150d4997446af869590fb5099e99783.png" alt="0150d4997446af869590fb5099e99783.png" width="1126" height="341" class="jop-noMdConv">

* * *

# Lid Closure and Display Power Management

Since a laptop is being used as the server, the goal is to be able to close the lid without powering it off, for storage and accessibility purposes.

## Disable Suspend on Lid Close

In the Web GUI, navigate to the node → **Shell**.  
<img src="../_resources/562691db121eb175978808c6e361202d.png" alt="562691db121eb175978808c6e361202d.png" width="1126" height="678" class="jop-noMdConv">

In the shell, open the login daemon configuration:

```bash
nano /etc/systemd/logind.conf
```

Find `#HandleLidSwitch=suspend`, uncomment it and change the value to `ignore`. Also find `#HandleLidSwitchDocked=ignore` and uncomment it. Press **Ctrl+X** to exit, **Y** to confirm, and **Enter** to save.  
<img src="../_resources/77d33bc1e3cbb0d33ad32c29af244d59.png" alt="77d33bc1e3cbb0d33ad32c29af244d59.png" width="1126" height="909" class="jop-noMdConv">

Now restart the `logind` service to apply the change:  
<img src="../_resources/9d2dc4529399df41d1fe63cf17ae7832.png" alt="9d2dc4529399df41d1fe63cf17ae7832.png" width="1126" height="248" class="jop-noMdConv">

## Configure Console Blanking

To turn off the laptop's virtual console display after a period of inactivity, open the GRUB configuration:

```bash
nano /etc/default/grub
```

Find the line `GRUB_CMDLINE_LINUX=""` and change it to:

```
GRUB_CMDLINE_LINUX="consoleblank=300"
```

This causes the TTY console to blank after 300 seconds (5 minutes) of inactivity.

> **Note:** `consoleblank` only affects the local virtual console (TTY), not the Proxmox web GUI session in your browser. The web GUI has its own independent session timeout.

Press **Ctrl+X** to exit, **Y** to confirm, and **Enter** to save.  
<img src="../_resources/a74a978cbbe06210c85abf9b71ed2ece.png" alt="a74a978cbbe06210c85abf9b71ed2ece.png" width="1126" height="347" class="jop-noMdConv">

Next, apply the GRUB configuration update:

```bash
update-grub
```

<img src="../_resources/2e769676c7fda7b33030f737c647d04f.png" alt="2e769676c7fda7b33030f737c647d04f.png" width="1126" height="341" class="jop-noMdConv">

* * *

# Removing LVM

Since this lab is running on limited hardware resources, logical volume partitioning may not be the best use of available disk space. By default, Proxmox creates two storage volumes under the node: `local-lvm` for virtual machine disks and containers, and `local` for backups, ISOs, and container templates.  
<img src="../_resources/acefe2dee1d1d5f6aa5c726baf6c4650.png" alt="acefe2dee1d1d5f6aa5c726baf6c4650.png" width="1126" height="248" class="jop-noMdConv">  
<img src="../_resources/f9b39a371fa64ae7972780fc136aad24.png" alt="f9b39a371fa64ae7972780fc136aad24.png" width="1126" height="246" class="jop-noMdConv">

> ⚠️ **Warning:** The steps below are destructive and irreversible. Ensure any existing virtual machines or containers have been backed up before proceeding.

To remove the `local-lvm` partition and consolidate all storage under `local`, first assign disk images and containers to `local`. Under the **Datacenter** view, navigate to **Storage**, select `local`, and click `Edit`.  
<img src="../_resources/9b0f16cdaac51293b952a70626991610.png" alt="9b0f16cdaac51293b952a70626991610.png" width="1126" height="618" class="jop-noMdConv">

In the dialog box, navigate to the **Content** dropdown and select `Disk Image` and `Container`. Click `OK`.  
<img src="../_resources/07c3a8c653c9c9a4aa55e65001160c7d.png" alt="07c3a8c653c9c9a4aa55e65001160c7d.png" width="1126" height="436" class="jop-noMdConv">

Next, select `local-lvm` and click `Remove`. Select `Yes` on the confirmation prompt.  
<img src="../_resources/0719d3db8f0215c7a24ec4aaee33f3e4.png" alt="0719d3db8f0215c7a24ec4aaee33f3e4.png" width="1126" height="430" class="jop-noMdConv">

Now expand the `local` storage to use the freed disk space. Navigate to the node shell and run the following commands:

```bash
# Delete the "data" logical volume and free its space
lvremove /dev/pve/data -y

# Expand the "root" logical volume to use all freed space
lvresize -l +100%FREE /dev/pve/root

# Resize the filesystem to match the new volume size
resize2fs /dev/mapper/pve-root
```

<img src="../_resources/cd9b223300a9c9364e7dc99eb13de5d7.png" alt="cd9b223300a9c9364e7dc99eb13de5d7.png" width="1126" height="356" class="jop-noMdConv">

Under **Summary**, the reported disk space should now reflect close to the full size of the physical disk.  
<img src="../_resources/5548cb67ad786499a3d1f2252589a999.png" alt="5548cb67ad786499a3d1f2252589a999.png" width="1126" height="426" class="jop-noMdConv">

* * *

# Adding ISO Images for Virtual Machines

To add ISO images for virtual machines, navigate to **local** → **ISO Images** and click either `Upload` (if the image is already on your working machine) or `Download from URL`.  
<img src="../_resources/f24fae1be6a77439aa3c44e399573c3d.png" alt="f24fae1be6a77439aa3c44e399573c3d.png" width="1126" height="373" class="jop-noMdConv">

Click `Upload`, choose the path to your ISO file, fill in the necessary details, and click `Upload`.  
<img src="../_resources/167cd927796fbc7071677362d36a7fa6.png" alt="167cd927796fbc7071677362d36a7fa6.png" width="1126" height="355" class="jop-noMdConv">

A successful upload will display the dialog below.  
<img src="../_resources/b159bcdeaae7160621a28f5b0178761c.png" alt="b159bcdeaae7160621a28f5b0178761c.png" width="1126" height="526" class="jop-noMdConv">

Repeat this process for any additional ISO images required for your lab environment.