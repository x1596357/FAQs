# FAQs
Just some questions and answers I want to keep around. I don't want it to be as heavy as blog. You might find some answers here. If you find this page is too long to read, try using find(Ctrl+F).

#### 1. How come my linux man pages are not complete?

try update mandb, issue `mandb` command with root privilidge.

`sudo mandb`

#### 2. How to use automake and autoconf?
1. create {src,docs,examples,...} folder in your working directory

 `# mkdir src`

2. create your source code

 `# vi src/hello.c`

3. run `autoscan` to create configure.ac
 ```
 # autoscan
 # mv configure.scan configure.ac
 ```
4. fill in information of your program in `AC_INIT()`, add `AM_INIT_AUTMAKE` the next line, and add output files `Makefile` and `src/Makefile` and etc to `AC_OUTPUT()` at the end. Static and dynamic libraries need some other informations. Check other material.
 ```
 AC_INIT(hello,1.0,hello@hello.com)
 AM_INIT_AUTOMAKE
 ...
 AC_OUTPUT(Makefile src/Makefile)
 ```
5. create `Makefile.am` in your working directory and in subdirectories like `src/` accordingly(applications or libraries).

 `# vi Makefile.am`

 with content
 ```
 AUTOMAKE_OPTIONS = foreign
 SUBDIRS = src
 ```
 `# vi src/Makefile.am`

 with content(application)
 ```
 bin_PROGRAMS = hello
 hello_SOURCES = hello.c
 ```
 with content(dynamic),check other materials for how to write for dynamic libraries and static libraries
 ```
 bin_PROGRAMS = hello
 hello_SOURCES = hello.c
 ```
6. use `libtoolize` if you are creating libraries

 `# libtoolize -f -c`

7. run `aclocal`,`autoheader`,`automake`,`autoconf` in order
 ```
 # aclocal
 # autoheader
 # automake -a
 # autoconf
 ```
You should have `./configure` scripts after this. If transfer these files to other system, you may want to run `autoreconf` and redo #7. Now you can do `./configure;make;make install;make clean;`

#### 3. How to create and apply patch?

 1. create
 
  `# diff -Nur old/ new/ >%{name}-%{version}-%{fixwhat}.patch`

  eg.

  ` # diff -Nur hello/ new_hello/ > hello-1.0-fixprint.patch`
  
 2. apply 
 
  `# patch -p0 < %{patchname}.patch`

  or this one. Recommand following one because base directory name may differ.
  ```
  # cd source_dir/
  # patch -p1 < ../%{patchname}.patch
  ```
  
#### 4. How to fix my Linux system even if `boot` partition is destroyed?

 1. Insert a livecd usbstick or dvd (I am using Fedora here). You search how to create one.

 2. Mount your need-to-be-fix system at `/mnt`

 3. Format and mount `boot` partition or make a `boot` folder in `/`
 ```
 # mkfs.ext4 /dev/sdx?(where boot partition is)
 # mount /dev/sdx? /mnt/boot
 ```
 or
 
 `# cd /mnt/ && mkdir /mnt/boot/`
 
 3. Install kernel,dracut(for mkinitrd) and grub2(for grub2-install and grub2-mkconfig),you need to connect to the Internet. Or you can install downloaded rpms of them.
 
 `sudo dnf install --installroot /mnt kernel grub2 dracut`

 You should have something like `vmlinuz` in your `/mnt/boot` directory
 
 4. Chroot
 ```
 # mount --bind /dev/ /mnt/dev/
 # mount --bind /sys/ /mnt/sys/
 # mount --bind /proc /mnt/proc/
 # chroot /mnt
 ```
 5. Make init ramdisk using mkinitrd, you can try `mkinitrd` get the prompt.
 
 `# mkinitrd /boot/initramfs-4.5.5-201.fc23.x86_64.img 4.5.5-201.fc23.x86_64`

 6. Install bootloader
 ```
 # grub2-install /dev/sdx(Harddisk device, not partition)
 # grub2-mkconfig > /boot/grub2/grub.cfg
 ```
 You should see some entries is there in `grub.cfg`. You might want to check `/etc/fstab` is correct and use `sudo chmod 755 /mnt` to make sure root have the corrent permission setting. You might have to temporarily disable `selinux` by setting `SELINUX=disabled` in `/etc/selinux/config`
 
#### 5. grep string in files?

 `grep -nrw <FOLDER_NAME> -e "STRING"` eg:
 
 `grep -nrw <>`
