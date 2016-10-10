---
layout: post
title: "ESXi Whitebox Lab Build"
categories: [esxi, whitebox, lab]
---
![ESXi Whitebox Lab Build]({{ site.url }}/img/esxi-whitebox-lab-build.jpg)

When testing new software, distros, or hacking the occasional [VulnHub](vulnhub) image - VMware Workstation (Windows) or VMware Fusion 
(OSX) will suffice. However, when something more permanent is needed, there is ESXi. [VMware’s ESXi](esxi), in my opinion, is a more 
mature and easier to configure hypervisor than its counterparts and is the reason I used it in this build.

### Requirements

I had a few requirements when researching this build, 1) keep the cost under $1500.00, 2) make sure power consumption is low. I 
live in an area where power is expensive, so with a server running 24/7 a high power draw machine would get expensive quickly, 
3) I needed something quiet, as the new server was going in my office. Finally, 4) I wanted a small form factor, so it could 
travel easier and potentially be used as a portable CTF server.

### Components

Motherboard - [ASRock C2750D4I](asrock) Intel Avoton C2750 ([$457.94](motherboard))
This board is supported by ESXi and includes a 2.4GHz Octa-Core processor, dual Intel i210 gigabit LAN ports, and a dedicated 
IPMI port for headless install and management. All packed onto a Mini-ITX board.

RAM - Crucial 32GB (8GBx4) DDR3/DDR3L-1600MT/s ECC UDIMM Server Memory ([$196.18](ram))
You have to be careful here, use only the RAM that is listed as supported on ASRock website or you might have trouble booting 
your machine. I opted for two of the 16GB kits, as a 64GB kit is extremely expensive.

Case - SilverStone Technology Mini-ITX/DTX Small Form Factor NAS Computer Case ($149.99)
I was on the fence about ordering this case, but when it arrived I was pleasantly surprised with how SilverStone was able to 
get eight hot-swappable bays in a compact case and the screened fan intakes are nice.

Power - Corsair SF450 SFX Form Factor ([$89.99](power))
The SFX power supply is necessary for the small form factor case, a traditional power supply will not fit. SilverStone makes 
power supply’s as well, but I happen to be a fan of Corsair.

Storage - 2x Seagate 2TB Desktop HDD SATA 6Gb/s 64MB Cache 3.5-Inch ([$134.94](storage))
These 2TB drives plus the addition of a few 120GB SSD’s that I had laying around gave me plenty of room and performance.

Flash Drive - 32GB Samsung USB 3.0 ([$10.50](flash-drive))
Install and boot ESXi from USB, so there is no need to use any space on my drives.

Cooling - Noctua A-Series Cooling Fan NF-A4x10 FLX ([$13.95](cooling))
This component is totally optional because the processor is passively cooled, but found that with this addition, core 
temperatures dropped by a few degrees. This was also and in-expensive add-on and still allowed me to come under budget. To 
attach the fan, I used the rubber pins that came in the box and pushed them into processor heatsink.

The grand total of my build before taxes was $1053.49, which was several hundred dollars under budget. The savings was due 
to having SSD’s lying around, otherwise I would have come in on the targeted budget by purchasing one or two.

### ESXi Installation

Download a copy of ESXi from VMware’s website, after registering, I chose version 6.0. At this point you need two USB flash 
drives, 1) to make bootable with the ESXi image that was downloaded. I found that using Rufus made this easy. Then, 2) the 
other flash drive, the 32GB USB ordered as part of the build, needs to be inserted into a USB slot on the back of the case. 
This is the drive that ESXi is going to be installed on. 

While the bootable flash drive was being created I downloaded updated firmware drivers for the BIOS, IPMI, and anything else 
I could find. Then I connected to the server via IPMI to perform the firmware updates. Next I proceeded with the ESXi 
installation onto the 32GB flash drive that was plugged into a USB port on the back of the case.

During installing and after booting ESXi I noticed version 6.0 did not recognize my SATA controllers, so ESXi could not see 
my drives. As it turns out VMware removed support for commodity network cards and SATA controllers in version 5.5. 
Fortunately, thanks to [Andreas Peetz](blog-post) blog post, there is a way to add support by running the below commands in 
and ESXi shell:

{% highlight bash %}
esxcli software acceptance set --level=CommunitySupported
esxcli network firewall ruleset set -e true -r httpClient
esxcli software vib install -d http://vibsdepot.v-front.de -n sata-xahci
{% endhighlight %}

After running the above commands and a reboot of the machine, the SATA controllers recognized my installed drives with no 
further issues. I also recommend turning off the HTTP Client in the firewall ruleset after the above updates have been completed. 

Overall I have been extremely happy with this build, it has been running my pentest lab with several domain controllers, a 
mix of Windows and Linux DNS servers, and various OS client machines without issue. I have also been very pleased with the 
low noise and power consumption. If you are looking for a fairly in-expensive, low noise, low power draw machine, then look 
no further.

[vulnhub]: https://www.vulnhub.com/
[esxi]: http://www.vmware.com/products/vsphere-hypervisor.html
[asrock]: http://www.asrockrack.com/general/productdetail.asp?Model=C2750D4I#Specifications
[blog-post]: http://www.v-front.de/2013/11/how-to-make-your-unsupported-sata-ahci.html
[motherboard]: https://www.amazon.com/gp/product/B00HIDQG6E/ref=oh_aui_detailpage_o02_s03?ie=UTF8&psc=1
[ram]: https://www.amazon.com/gp/product/B008EMA5VU/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1
[case]: https://www.amazon.com/gp/product/B00IAELTAI/ref=oh_aui_detailpage_o02_s01?ie=UTF8&psc=1
[power]: https://www.amazon.com/gp/product/B01CGI5M24/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1
[storage]: https://www.amazon.com/gp/product/B005T3GRN2/ref=oh_aui_detailpage_o02_s03?ie=UTF8&psc=1
[flash-drive]: https://www.amazon.com/gp/product/B013CCTM2E/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1
[cooling]: https://www.amazon.com/gp/product/B009NQLT0M/ref=oh_aui_detailpage_o09_s00?ie=UTF8&psc=1
