<h1>Arch Linux Installation (Need Internet Connection)</h1>
<p>Installation Guide from Main(<a href="https://wiki.archlinux.org/index.php/installation_guide">archlinux.org</a>)</p>

<img src="https://user-images.githubusercontent.com/66734606/111743490-2cafa280-88b8-11eb-8582-a369ca47909c.png" width="700px" height="350px">

<b><h3>!!WARNING!! Make Sure that you have backuped you files!! I recommend to try first it with Virtualbox</h3></b>
<b><h3>Arch Linux Image Doesn't Support Secure Boot.You Have To Disble Secure Boot In BIOS Settings</h3></b>
<br><br>
You Can Download Installation Image at <a href="https://archlinux.org/download/">Here</a>(<a href="https://archlinux.org/download/">Arch Linux</a>).After Download,Make Bootable USB With <a href="https://www.balena.io/etcher/">BalenaEtcher</a> or use <code>dd bs=4M if=/path/to/archlinux.iso of=/dev/sdx status=progress && sync</code>
<br><br><br>
To Boot Into Live Medium,Select Boot Arch Linux (x86_64).<br>
<img src="https://user-images.githubusercontent.com/66734606/111743715-81531d80-88b8-11eb-8c42-f4cdcd67cd13.jpg" width="600px" height="300px">
<br>
After Checking Process,You Will Get Shell With Root Permission(auto login).To Check UEFI Mode,Run ls command like this.<br>
<code>ls /sys/firmware/efi/efivars</code><br>
If This Command Has No Error,Your System Has Enabled UEFI Mode.Some Steps Are Different For UEFI and Legacy.<br>
<br><br>
<b>Connect To The Internet</b><br>
I recommend Ethernet Connection.For Connecting Wifi(if wireless card is available),<br>
<code>$ iwctl device list</code><br>
<code>$ iwctl station DEVICE scan</code><br>
<code>$ iwctl station DEVICE get-networks</code><br>
<code>$ iwctl --passphrase=PASSPHRASE station DEVICE connect SSID</code><br>
<br><br>
<b>Partition The Disk</b><br>
To list all disk and partitions,use <br>
<code>fdisk -l</code><br>
You Will Get Labelled /dev/sda or /dev/sdb./dev/sda is more common.So, To Partition the disk
<code>fdisk /dev/sda</code><br>
Must Create ESP Partition For UEFI System.(UEFI Only)If Not,Can Skip This Step.<br>
Type <i>m</i> for help.
To Create New Partition, Enter <i>n</i>.Select Partition Number.Hit Enter First Sector.Enter <i>+512M</i> in Last Sector.<br>
<br><br>
<img src="https://user-images.githubusercontent.com/66734606/111743780-9465ed80-88b8-11eb-9510-70c7cfe14f68.png" width="500px" height="110px">
<br><br>
To Change Parition Type, Enter <i>t</i> .And Enter L to list all parition types.Enter Number of EFI Parition Type<br>
<img src="https://user-images.githubusercontent.com/66734606/111743824-a34ca000-88b8-11eb-8125-43ffeb3a851f.jpg">
<br><br>
<b>Create Root Partition <u>(For Both UEFI and Legacy Systems)</u></b><br>
In the <code>fdisk</code> Command,Enter Partition Number,First Sector.
And I Enter The Reset of HDD for Last Sector <b>+10G</b><br>
After All Of This,Enter w To Write Changes On Disks.
<br><br>
<img src="https://user-images.githubusercontent.com/66734606/111743859-b0698f00-88b8-11eb-9d8e-bcdb9227475c.jpg">
<br>
<i><h5>Summary for Partition:</h5>For UEFI System,We Need EFI Partition and Root Partition.<br>For Legacy System,We Only Need Root Partition.</i>
<br><br>
<b>Create File System</b><br>
Use <code>mkfs</code>command:<br>
<h5>For UEFI System</h5>
<code>mkfs.fat -F32 /dev/sda1</code> (For EFI Partition)<br>
<code>mkfs.ext4 /dev/sda2</code> (For Root Partition)
<br>
<h5>For Legacy System</h5>
<code>mkfs.ext4 /dev/sda1</code> (For Only One Partition {Root})<br>
<br><br>
<b>Install Arch Linux</b><br>
Sync <i>pacman</i> Repository with:
<code>pacman -Syy</code><br>
Mount Root System Partition with:
<code>mount /dev/sda2 /mnt</code>(It is Root Partition!!)<br>
To Install Necessary Packages,Use <a href="https://git.archlinux.org/arch-install-scripts.git/tree/pacstrap.in">pacstrap</a> Script.<br>
Command:
<code>pacstrap /mnt base base-devel linux linux-firmware vim nano</code><br>
This will take some time.Take a coffee ;).If it is interrupted,Run this command again.<br>
<br>
<b>Configure Linux System</b><br>
Generate a fstab file to define how disk partitions or block devices are mounted into the filesystem.<br>
Command :<code>genfstab -U /mnt >> /mnt/etc/fstab</code><br>
Use <i>arch-chroot</i> And Enter the Mounted Disk As Root.
Command :<code>arch-chroot /mnt</code><br>
We Are Using Live Medium.After this command,We Get Root Shell To Configure The Installed System For Working Well On Boot Disk.<br>
<br><br>
<b>Setting Timezone</b><br>
To list all timezones,Use : <code>timedatectl list-timezones</code>
<br>
For Us (Myanmar Timezone),Enter : <code>timedatectl set-timezone Asia/Yangon</code><br>
<br><br>
<b>Set Locale</b><br>
This is For Language,Numbering,Date and Currency Formats On System.<br>
Use : <code>nano /etc/locale.gen</code> or <code>vim /etc/locale.gen</code> To Edit locale.gen File.And Uncomment en_US.UTF-8 UTF-8 (For English With US).<br>
Generate <b>locale</b> config file in <b>/etc</b> Directory By Running The Following Comands One By One:<br>
<code>locale-gen</code><br>
<code>echo LANG=en_GB.UTF-8 > /etc/locale.conf</code><br>
<code>export LANG=en_GB.UTF-8</code><br>
<br><br>
<b>Network Configuration</b><br>
Create <i>hostname</i> File In /etc Directory For PC Name By Command : <code>echo localarch > /etc/hostname</code><br>
You Can Use Different Name For Your PC.<br>
Edit hosts File By Command: <code>/etc/hosts</code><br>
And Write Like The Following :<br>a
<b>127.0.0.1	localhost<br>
::1		localhost<br>
127.0.1.1	myarch</b><br>
<br><br>
<b>Set Root Password And Create New User</b><br>
Set Root Password By Command : <code>passwd</code><br> And Enter Your Password Twice.<br>
<br>
Create New User<br>
Enter The Commands One By One:
<code>useradd -m username</code><br>
<code>usermod -aG wheel username</code><br>
<code>nano /etc/sudoers</code><br>
Find and Uncomment this line : <b>%wheel ALL=(ALL) ALL</b><br><br>
<br>
<b>Install GRUB Bootloader</b>
<h5>For UEFI System</h5>
Install grub and required packages:
<code>pacman -Sy grub efibootmgr</code><br>
Make /boot/efi Directory :
<code>mkdir /boot/efi</code><br>
Mount EFI Partition :
<code>mount /dev/sda1 /boot/efi</code><br>
Install GRUB :
<code>grub-install --target=x86_64-efi --bootloader-id=GRUB --efi-directory=/boot/efi</code><br>
Generate GRUB Config File :
<code>grub-mkconfig -o /boot/grub/grub.cfg</code>
<br><br>
<h5>For Legacy System</h5>
Install GRUB package:
<code>pacman -S grub</code><br>
Install GRUB Bootloader :
<code>grub-install /dev/sda</code><br>
Make Config File :
<code>grub-mkconfig -o /boot/grub/grub.cfg</code><br>
<br><br>
<b>Install Desktop Environment (GUI)</b>
You Can Install DE You Like.(xfce,mate,kde,gnome,cinnamon,etc)<br>
Also You Can Change DE Later.<br><br>
The Following Command Install GNOME Desktop Environment.<br>
<code>pacman -Sy xorg gnome</code><br>
Enable Display Manager (gdm) and NetworkManager :<br>
<code>systemctl start gdm.service</code><br>
<code>systemctl enable gdm.service</code><br>
<code>systemctl enable NetworkManager.service</code><br>
Exit The Current Shell From Chroot : <code>exit</code><br>
Shutdown System : <code>shutdown</code><br>

<b>Referrence : </b><a href="https://wiki.archlinux.org/index.php/Installation_guide">wiki.archlinux.org</a><br>
<b>YouTube Video : </b><a href="https://youtu.be/cM2UDz8BepU">Arch Linux Installation (Open Source Home)</a><br>
