---
layout: post
title: Chia Coin and Setup WD Duo
---

Recently the supply of mechanical drive is limited and the price surge up to almost double, due to the fever of cryptocurrency Chia Coin. The Chia coin is a cryptocurrency invented by the founder of BitTorrent, utilise storage space instead of cpu mining to reduce the energy consumption of the mining process, but the China (The name has nothing to do with China actually) miner already buying used storage servers and new hard drives to make a profit from it. The coin itself hasn't available for exchange yet and is currently in mining stage only. It looks interesting and worth a try if you have some existing hardware. It requires you to have good CPU and RAM for plotting stage, and ONLY storage space for farming stage.

Price a few days ago
![_config.yml]({{ site.baseurl }}/images/2021-04-20-previous-price.jpg)
Price almost doubled today
![_config.yml]({{ site.baseurl }}/images/2021-04-20-harddisk-price.jpg)


I have a DIY NAS setup that containing my data archive. On the other hand I've purchased a 2nd hand NUC for Linux desktop use. It seems to me that both are very good candidates to try mining Chia coin, however it will requires extra storage space to play with it. Therefore I come up an excuse to purchase an external drive to 'backup' my data. I bought a WD Book Duo 24TB due to the following reasons:
 - It match the storage size of my NAS, so I don't have to worry about space
 - It is a RAID 0 setup by default which provides performance gain, good for mining
 - It is USB Type-C gen 1, providing 5Gbps speed which should be sufficient
 
My plan is to:
 1. Make a copy of the NAS data to the WD Duo
 2. Reinstall the NAS to run latest Ubuntu server instead of Kali Linux
 3. Clean up the data on the WD Duo, mine the chia coin using the WD Duo + NUC
 
I don't prefer to use my NAS for mining due to the following reasons:
 - NAS is RAID-5 based setup which the performance is not as good as the WD Duo RAID 0
 - The NAS provide better data security as it is RAID-5 based
 - USB type-C would not be as stable as the NAS as it is connected via SATA directly
 - The NUC + Chia provide better mining performance over the NAS as the CPU is newer
 
Here is the photo of the WD Duo
![_config.yml]({{ site.baseurl }}/images/2021-04-20-wd-duo-box.jpg)

And what is looks when connected with my NAS
![_config.yml]({{ site.baseurl }}/images/2021-04-20-nas-wd-duo.jpg)

And photo of the NAS with the WD Duo connected after unboxing



 
For this post I would briefly explain how to setup the WD Duo to backup the data. The requirement of the data backup are:
 - Fully encrypted
 - Linux ext4

First we have to connect it physically to the NAS via USB
```
charles@tanker:~$ lsblk                                                                                                                                                                            
NAME              MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT                                                                                                                                             
<REDACTED>
...       
</REDACTED>                                                                                    
sdf                 8:80   0 21.8T  0 disk                                                                                                                                                         
└─sdf1              8:81   0 21.8T  0 part   

sudo -s

root@tanker:~# parted --align optimal /dev/sdf                                                                                                                                                                                                                                                                                                                                                        
GNU Parted 3.4                                                                                                                                                                                     
Using /dev/sdf                                                                                   
Welcome to GNU Parted! Type 'help' to view a list of commands.                                    

(parted) mklabel gpt
Warning: The existing disk label on /dev/sdf will be destroyed and all data on this disk will be lost. Do you want to continue?
Yes/No? yes          

(parted) p                                                                                                                                                                                         
Model: WD My Book Duo 25F6 (scsi)                                                                                                                                                                  
Disk /dev/sdf: 24.0TB                                                                                                                                                                              
Sector size (logical/physical): 512B/4096B                                                                                                                                                         
Partition Table: gpt                                                                                                                                                                               
Disk Flags:                                                                                      
                                                                                                 
Number  Start  End  Size  File system  Name  Flags                                               
                                                                                                 
(parted) mkpart non-fs 1 100%                                                                                                                                             

(parted) p                                                                                                                                                                                         
Model: WD My Book Duo 25F6 (scsi)                                                                                                                                                                  
Disk /dev/sdf: 24.0TB                                                                                                                                                                              
Sector size (logical/physical): 512B/4096B                                                                                                                                                         
Partition Table: gpt                                                                                                                                                                               
Disk Flags:                                                                                      
                                                                                                 
Number  Start   End     Size    File system  Name    Flags                                                                                                                                          
1      1049kB  24.0TB  24.0TB               non-fs                                                                                                                                                                                                                                                                                                                                                   

(parted) q                                                                                                                                                                                         
Information: You may need to update /etc/fstab.

``` 

Note that we need to specify the parameter ```--align optimal``` to achieve best performance.

Then we setup the disk encryption using LUKS and open it. We use password to encrypt here.

```
root@tanker:~# cryptsetup luksFormat /dev/sdf1
WARNING: Device /dev/sdf1 already contains a 'ntfs' superblock signature.

WARNING!
========
This will overwrite data on /dev/sdf1 irrevocably.

Are you sure? (Type 'yes' in capital letters): YES
Enter passphrase for /dev/sdf1: 
Verify passphrase: 
root@tanker:~# cryptsetup luksOpen /dev/sdf1 backup
Enter passphrase for /dev/sdf1: 
root@tanker:~#
```
a crypt disk will be shown under the sdf1 partition
```
charles@tanker:~$ lsblk
NAME              MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT
<REDACTED>
...       
</REDACTED>      
sdf                 8:80   0 21.8T  0 disk  
└─sdf1              8:81   0 21.8T  0 part  
  └─backup        253:2    0 21.8T  0 crypt

```
now we can format it and mount it up. It take a minute to complete the ext4 format.
```

root@tanker:˜# mkfs.ext4 /dev/mapper/backup 
mke2fs 1.46.2 (28-Feb-2021)
Creating filesystem with 5859422720 4k blocks and 366215168 inodes
Filesystem UUID: <REDACTED.
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
        102400000, 214990848, 512000000, 550731776, 644972544, 1934917632, 
        2560000000, 3855122432, 5804752896

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done         

```

Now we open a tmux session and start mount and sync files from NAS to the backup disk. It will take quite a while to complete...

```root@tanker:~# mkdir /mnt/backup; mount /dev/mapper/backup /mnt/backup
root@tanker:~# rsync --progress -av /storage /mnt/backup
```





