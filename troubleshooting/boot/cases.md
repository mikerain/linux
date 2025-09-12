#### What to do if a server fails to boot?

https://access.redhat.com/articles/5250481



After update, kernel panic at boot with error: Unable to mount root fs on unknown-block(0,0)

https://access.redhat.com/solutions/57018



Server Booted into Emergency Mode

https://access.redhat.com/support/cases/#/case/04220486



RHEL 8 Server Unable To Boot

https://access.redhat.com/support/cases/#/case/04234387





```
Thanks for your time over the call, below is the summary :

[1] Server was failing to boot because unable to mount /boot/efi and landing into `Ctrl-D to continue` prompt.
    When we checked in the server we found that `vfat module was not loaded` because it was not present under `/lib/modules/5.14.0-570.32.1.el9_6.x86_64/kernel/fs/fat/*` (not found error)
	
[2] When I joined the remote session server was landing into dracut prompt and no partitions OR LV's were visible in it, hence we booted it into rescue mode.

[3] In rescue mode we checked below things :
    - # ls -l /lib/modules/5.14.0-570.32.1.el9_6.x86_64/kernel/fs/fat/vfat.ko.xz
	- # lsinitrd /boot/initramfs-5.14.0-570.32.1.el9_6.x86_64.img | grep vfat
	In both no output came
	
[4] As the kernel module itself was not there we copied the kernel-core-5.14.0-570.32.1.el9_6.x86_64 and reinstalled wih below command :
    - # rpm -ivh --replacefiles --replacepkgs --root=/mnt/sysroot kernel-core-5.14.0-570.32.1.el9_6.x86_64.rpm   <<<=== this we used in rescue mode only.

   (In case when server is booted in normal mode we can use :- # yum reinstall kernel-core-5.14.0-570.32.1.el9_6.x86_64)   >>>>> Can be use in other srver with same issues which can boot normally   <<<<<
   
[5] After this kernel module vfat become available.
    We then regenerated initramfs file:
	- # dracut -fv /boot/initramfs-5.14.0-570.32.1.el9_6.x86_64.img 5.14.0-570.32.1.el9_6.x86_64
	
	But still initramfs does not included the `vfat` module in it.
	
[6] Now we rebuild initramfs to manually add/forcefully adding th vfat module 

	- # dracut -fv --add-drivers "vfat" /boot/initramfs-5.14.0-570.32.1.el9_6.x86_64.img 5.14.0-570.32.1.el9_6.x86_64

    Now the module got included into initramfa file.
	
[7] We rebooted the server and its' booted fine AND also able to mount /boot/efi.
```



How to rebuild the initial ramdisk image in Red Hat Enterprise Linux

https://access.redhat.com/solutions/1958	



What is 'Signal 15' ?

https://access.redhat.com/solutions/737033



Why does Red Hat Enterprise Linux Server reboot with "signal 15" messages in /var/log/messages?

https://access.redhat.com/solutions/31411



How to find which user has rebooted the system?

https://access.redhat.com/solutions/41281



How to boot Red Hat Enterprise Linux to Rescue Mode for Data Collection (sosreport, vmcore, etc.)

https://access.redhat.com/articles/3405661



How to troubleshoot kernel crashes, hangs, or reboots with kdump on Red Hat Enterprise Linux

https://access.redhat.com/solutions/6038



```
Failed to mount /boot/efi.
```

https://access.redhat.com/support/cases/#/case/03221684



```
rebuild the initramfs with multipath in
```

https://access.redhat.com/solutions/135713

https://access.redhat.com/support/cases/#/case/03204182

```
>>>> How can we avoid this same problem during the next patch cycle ? 

ANS. Prior patching we can perform backup of old initramfs file as below. 

# cp -p /boot/initramfs-3.10.0-693.2.2.el7.x86_64.img /boot/initramfs-3.10.0-693.2.2.el7.x86_64.img.bkp
```





How to generate BLS configuration files under /boot/loader/entries in Red Hat Enterprise Linux?

https://access.redhat.com/solutions/5847011



How to repair filesystem in rescue environment for Red Hat Enterprise Linux?

https://access.redhat.com/solutions/9541

```
# lsblk
# lvs -ao+devices
# vgs
# xfs_repair -n /dev/sda2
```



