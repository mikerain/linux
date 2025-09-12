

```
# rpm -Va |& tee /tmp/rpmva.txt     
     # grep -v " c " /tmp/rpmva.txt | awk '{print $NF}' | sort | uniq | xargs rpm -qf 2>/dev/null | sort | uniq &> /tmp/pkgs.txt  

- Execute the upgrade again using the --debug option.  DO NOT REBOOT after this process completes. 

     # redhat-upgrade-tool --debug   <----------please modify as needed but be sure to keep the --debug option

- Once this completes, attach the files below to the case for review. 

     /var/log/upgrade.log
     /root/preupgrade/result.html
```



which file should be backed up before upgrade

how to rollback to old version

how to deal with the boot failure after upgrade





reintall  grub

```
1. Boot system to rescue.

   How to boot Red Hat Enterprise Linux to Rescue Mode for Data Collection (sosreport, vmcore, etc.)
   https://nam10.safelinks.protection.outlook.com/?url=https%3A%2F%2Faccess.redhat.com%2Farticles%2F3405661&amp;data=05%7C01%7Cwale.ajayi%40quorumsoftware.com%7C8bd32cd84a2643e572c108da3e671bc8%7Cce68f836c22145ef866b38cda86b3d5e%7C0%7C0%7C637890909849661238%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C3000%7C%7C%7C&amp;sdata=kcOlXY%2BXzM8eKkjtQWbb09K%2Bqq6MJL8Hio1yU86QJ2k%3D&amp;reserved=0

2. Chroot

   # chroot /mnt/sysimage

3. Reinstall grub. Since we weren't able to use yum, due to networking issue, we will need to use rpm.

  A) Lets test the reinstall using the command below. If you do not see error reported then proceed with the next command.

     # rpm -ivh --replacepkgs --replacefiles --test grub2-efi-x64-2.02-0.86.el7_8.x86_64.rpm shim-x64-15-7.el7_8.x86_64.rpm

  B) Reinstall grub without test option

    # rpm -ivh --replacepkgs --replacefiles grub2-efi-x64-2.02-0.86.el7_8.x86_64.rpm shim-x64-15-7.el7_8.x86_64.rpm

4. Run the command below to add new uefi boot entry. If you get an error then upload it to the case.

   # efibootmgr -c -d /dev/sda -p 1  -l \\EFI\\redhat\\shimx64.efi -L "Red Hat Enterprise Linux 7"

5. Reboot system normally and see if you are seeing grub menu.
```