# terra-master-os-on-vm
Information to run TOS or Terra-Master OS in a VM

## Requirements
- QEMU-KVM running Q35 in UEFI mode
- Virt Manager or QEMU at the command line
- Appropriate files from terra-master.com to run the vm
  Files used in this example are:
    USB_Initboot_UEFI-229.img - UEFI Boot Partition (DD to 32GB qemu image) acquired from https://forum.terra-master.com/en/viewtopic.php?t=6433
    TOS_X642.0_5.1.145_00320_2407200647.ins - TOS installation image acquired from https://support.terra-master.com/download/packages?product=F2-423

## Image preparation
  - Create a 32GB image with the following command:
     qemu-img create -f qcow2 tnas-usb-32gb.img 32G
  - Attach the tnas-usb-32gb.img to a qemu vm booting Ubuntu 24.04 LTS live image.
  - Boot up the vm and skip the installation of ubuntu.
  - Use the disk utility to partition to restore the USB_Initboot_UEFI-229.img to the drive.
  - Use the disk utility to create a second partiton that is the rest of the drive, leave file system as ext4.
  - Start up the terminal and run sudo -i to get root privileges.
  - Run the mkfs.ext2 command on the second partition. which should be /dev/sda2 in this example.
  - Run mkdir /mnt/usb
  - Mount the second partition to the /mnt/usb directory.
  - Copy the TOS_X642.0_5.1.145_00320_2407200647.ins file to the /mnt/usb directory.
  - Rename TOS_X642.0_5.1.145_00320_2407200647.ins to TOS_X642.0_5.1.145_00320_2407200647.tar.xz
  - cd /mnt/usb
  - Run xz -d TOS_X642.0_5.1.145_00320_2407200647.tar.xz
  - Run tar -xvf TOS_X642.0_5.1.145_00320_2407200647.tar
  - Then exit the terminal and shutdown the vm.
  - Now you'll have a working USB Image to use with a VM to run the TOS or Terra-Master OS.

If you want to skip these steps I'll include a prebuilt image that I made. It's named tnas-usb-32gb.img.xz and is in the repo. You'll need to decompress it first.

## Running the VM
- Running with Virt Manager
  - Open Virt Manager and create a new VM. Select Manual Install.
  - Just use genericlinux2022 or genericlinux2024 for the OS type.
  - Assign Memory and CPUs as needed. (I used 8192MB and 4 CPUs to mimic a F2-423)
  - Uncheck Enable storage for this virtual machine.
  - When prompted check Customize configuration before install. Then Click Finish.
  - Set Chipset to Q35 and Firmware to UEFI.
  - Change the NIC device to e1000e device instead of virtio for networking.
  - Change the Video virtio device model to vmvga. (This is required to see the console on boot otherwise scrambled text will appear that isn't readable.)
  - Add Hardware, then Add Storage, set Bus type to USB, and select the tnas-usb-32gb.img image.
  - You should also do this for two additional SATA storage devices. (I tried virtio/scsi but it didn't work, be advised USB devices don't show up for initial setup.)
  - Then "Begin Installation". At this stage you should be able to get to a login prompt on the VM.
  - At this stage you can use the IP address of the VM to connect to it http://<IP Address>/ and use the wizard to configure terra-master OS.

Note the security email code bit needs to be skipped for some reason it cannot send the code to your email address. I suspect because it's not a real terra-master NAS so it just doesn't work. 
  
