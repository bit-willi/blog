---
title: Setup hibernate on arch linux with grub
published: true
---


# Setting Up Hibernate on Arch Linux with GRUB


Hibernate is a power-saving state that allows you to save the contents of your RAM to disk and power off your machine completely. When you power it back on, the system state is restored, allowing you to continue from where you left off. This guide will help you set up hibernate on Arch Linux using GRUB.


## Step 1: Configure Initramfs


The initial RAM filesystem (initramfs) is a temporary root file system loaded into memory during the Linux startup process. To enable hibernate, we need to add the resume hook to the initramfs configuration.

Open the initramfs configuration file for editing:

```bash
sudo vim /etc/mkinitcpio.conf
```

Find the HOOKS array and add resume after filesystems. The line should look like this:

```txt
# Before
HOOKS=(base udev autodetect microcode modconf encrypt kms keyboard keymap consolefont block filesystems fsck)

# After
HOOKS=(base udev autodetect microcode modconf encrypt kms keyboard keymap consolefont block filesystems resume fsck)
```

Save the file and exit the editor.


## Step 2: Configure GRUB


GRUB (GRand Unified Bootloader) is a boot loader that you can use with Arch Linux. We need to add kernel parameters so the kernel can locate and restore the hibernation image.

First, gather the necessary information: the UUID of the swap partition or file and the resume offset (if using a swap file).

Get the UUID:

```bash
# If using a swap file
findmnt -no UUID -T /swap
# Example output: 38037066-a1d3-4179-a309-d325df1ccd02

# If using a swap partition (check /etc/fstab if needed)
cat /etc/fstab | grep 'swap' | tail -n 1 | awk '{print $1}'
# Example output: UUID=38037066-a1d3-4179-a309-d325df1ccd02
```

Get the resume offset

```bash
filefrag -v /swap | awk '{ if($1=="0:"){print substr($4, 1, length($4)-2)} }'
# Example output: 4161534
```

Edit the GRUB configuration file:

```bash
sudo vim /etc/default/grub
```

Modify the GRUB_CMDLINE_LINUX_DEFAULT line to include the resume parameters:

```txt
# Before
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"

# After
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash resume=UUID=38037066-a1d3-4179-a309-d325df1ccd02 resume_offset=4161534"
```

Save the file and exit the editor.


## Step 3: Update GRUB and Initramfs


After modifying the configuration files, you need to regenerate the initramfs and update the GRUB configuration.

Regenerate the initramfs:

```bash
sudo mkinitcpio -p linux
```

Update the GRUB configuration:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo grub-mkconfig -o /boot/efi/EFI/arch/grub.cfg
```


## Conclusion


By following these steps, you should have successfully set up hibernation on your Arch Linux system using GRUB. Hibernate can be a very useful feature for saving power and quickly resuming your work. If you encounter any issues, ensure that the UUID and offset values are correct and that the resume hook is properly configured in your initramfs.

If you need more details, take a look at [Arch wiki](https://wiki.archlinux.org/title/Hibernate#Hibernation)

