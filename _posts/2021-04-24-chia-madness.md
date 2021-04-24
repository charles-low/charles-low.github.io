---
layout: post
title: Chia madness
---
After inital trial of mining using laptops, the result is not very satisfying as it takes days to complete the first 4 plot. I gave myself an excuse to build a budgeted PC for mining Chia coin. Afterall I've been planning to build my own PC for gaming, just that the GPU was not available at all, it is a waste to build a PC and idle it. But mining chia 

The another excuse is my 4 bay NAS halt when I tried to backup all my data from the disk array to the newly bought WD Duo. It seems that the system was not stable enought under stress, I was frightened because all my data was stored in the NAS. The workaround was to transfer all data to another host thru ethernet, which the loading would not heavy enough to crash the NAS.

I've bought a budget build based on suggestion on the website. The build also put into consideration for future gaming. 
 - Intel i7-10700 
 - Asus TUF B560 Plus WiFi (instead of B4xx-series suggested)
 - Coolmaster CX750 Full Modular PSU
 - ADATA 16GBx2 DDR4 3200Mhz RAM
 - WD SN750 2TB NVMe
 - Fractal Design Meshify 2 ATX Case
 
It costed me around HKD10,000
 
I ordered a U.2 3.2TB NVMe on NewEgg but I regretted because of its cost (HKD8,000), and requested to cancel the order. Then I checked the price again and it is now raised to HKD8,800. What a madness it is. Not sure how will the order goes, I'm kind of okay if it still ship the order. But using the WD 2TB NVMe to plot the chia coin seems easier for me now.

I installed the newly build system with Ubuntu 20.04 to setup the Chia mining and encountered some issues:
 - The installer and system cannot boot up and the screen go blank. Need to workaround by setting "nomodeset" in grub bootloader
 - Tried to install 21.04 but the Chia install script isn't ready for it
 - Mistakenly deleted 4 plot previous plotted as the node showing the plot file is not synchronised
 
After fiddling all the ZFS and partition for mining I am going to summarise the configuartion required for proper mining. 