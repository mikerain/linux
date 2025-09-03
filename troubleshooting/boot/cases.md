#### What to do if a server fails to boot?

https://access.redhat.com/articles/5250481





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

