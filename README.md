# nwipe
![GitHub CI badge](https://github.com/martijnvanbrummelen/nwipe/workflows/ci_ubuntu_latest/badge.svg)
![GitHub CI badge](https://github.com/martijnvanbrummelen/nwipe/workflows/ci_ubuntu_16.04/badge.svg)
[![GitHub release](https://img.shields.io/github/release/martijnvanbrummelen/nwipe)](https://github.com/martijnvanbrummelen/nwipe/releases/)

nwipe is a program that will securely erase disks. It can operate as both a command line
tool without a GUI or with an ncurses GUI as shown in the example below. It can wipe multiple
disks simultaneously.

The user can select from a variety of recognised secure erase methods which include:

* Zero Fill          - Fills the device with zeros, one round only.
* RCMP TSSIT OPS-II  - Royal Candian Mounted Police Technical Security Standard, OPS-II
* DoD Short          - The American Department of Defense 5220.22-M short 3 pass wipe (passes 1, 2 & 7).
* DoD 5220.22M       - The American Department of Defense 5220.22-M full 7 pass wipe.
* Gutmann Wipe       - Peter Gutmann's method (Secure Deletion of Data from Magnetic and Solid-State Memory).
* PRNG Stream        - Fills the device with a stream from the PRNG.
* Verify only        - This method only reads the device and checks that it is all zero.
* HMG IS5 enhanced   - Secure Sanitisation of Protectively Marked Information or Sensitive Information

It also includes the following pseudo random number generators:
* Mersenne Twister
* ISAAC

It is a fork of the dwipe command used by
Darik's Boot and Nuke (dban).  nwipe is included with [partedmagic](https://partedmagic.com) and
[ShredOS](https://github.com/nadenislamarre/shredos) if you want a quick and easy bootable CD or USB version. If you want a bootable version of the latest nwipe master that you can write to a USB stick, see [quick and easy bootable version of nwipe master](https://github.com/martijnvanbrummelen/nwipe#quick--easy-bootable-version-of-nwipe-master)

Nwipe was created out of a need to run the DBAN dwipe command outside
of DBAN, in order to allow its use with any host distribution, thus
giving better hardware support.

![Example wipe](/images/example_wipe.gif)

## Compiling & Installing

`nwipe` requires the following libraries to be installed:

* ncurses
* pthreads
* parted

and optionally, but recommended!, the following programs:

* dmidecode
* readlink
* smartmontools

### Debian & Ubuntu prerequisites

If you are compiling `nwipe` from source, the following libraries will need to be installed first:

```bash
sudo apt install \
  build-essential \
  pkg-config \
  automake \
  libncurses5-dev \
  autotools-dev \
  libparted-dev \
  dmidecode \
  coreutils \
  smartmontools
```

### Fedora prerequisites

```bash
sudo bash
dnf update
dnf groupinstall "Development Tools"
dnf groupinstall "C Development Tools and Libraries"
yum install ncurses-devel
yum install parted-devel
yum install dmidecode
yum install coreutils
yum install smartmontools
```
Note. The following programs are optionally installed although recommended. 1. dmidecode 2. readlink 3. smartmontools.

#### dmidecode
dmidecode provides SMBIOS/DMI host data to stdout or the log file. If you don't install it you won't see the SMBIOS/DMI host data at the beginning of nwipes log.

#### coreutils (provides readlink) 
readlink determines the bus type, i.e. ATA, USB etc. Without it the --nousb option won't work and bus type information will be missing from nwipes selection and wipe windows. The coreutils package is often automatically installed as default in most if not all distros.

#### smartmontools
smartmontools obtains serial number information for supported USB to IDE/SATA adapters. Without it, drives plugged into USB ports will not show serial number information.


If you want a quick and easy way to keep your copy of nwipe running the latest master release of nwipe see this section [automating the download and compilation](https://github.com/martijnvanbrummelen/nwipe#automating-the-download-and-compilation-process)

### Compilation

For a development setup, see [the hacking section below](#Hacking).

First create all the autoconf files:
```
./init.sh
```

Then compile & install using the following standard commands:
```
./configure
make
make install
```

Then run nwipe !
```
cd src
sudo ./nwipe
```
or
```
sudo nwipe
```

### Hacking

If you wish to submit pull requests to this code we would prefer you enable all warnings when compiling.
This can be done using the following compile commands:

```
./configure --prefix=/usr CFLAGS='-O0 -g -Wall -Wextra'
make
make install
```

The `-O0 -g` flags disable optimisations. This is required if you're debugging with
`gdb` in an IDE such as Kdevelop. With these optimisations enabled you won't be
able to see the values of many variables in nwipe, not to mention the IDE won't step
through the code properly.

The `-Wall` and `-Wextra` flags enable all compiler warnings. Please submit code with zero warnings.

Also make sure that your changes are consistent with the coding style defined in the `.clang-format` file, using:
```
make format
```
You will need `clang-format` installed to use the `format` command.


Once done with your coding then the released/patch/fixed code can be compiled,
with all the normal optimisations, using:
```
./configure --prefix=/usr && make && make install
```
### Automating the download and compilation process for Debian based distros.

Here's a script that will do just that!. It will create a directory in your home folder called 'nwipe_master'. It installs all the libraries required to compile the software (build-essential) and all the libraries that nwipe requires (libparted etc). It downloads the latest master copy of nwipe from github. It then compiles the software and then runs the latest nwipe. It doesn't write over the version of nwipe that's installed in the repository (If you had nwipe already installed). To run the latest master version of nwipe manually you would run it like this `sudo ~/nwipe_master/nwipe/src/nwipe`

You can run the script multiple times, the first time it's run it will install all the libraries, subsequent times it will just say the the libraries are upto date. As it always downloads a fresh copy of the nwipe master from Github, you can always stay up to date. Just run it to get the latest version of nwipe. It takes all of 11 seconds on my I7.

If you already have nwipe installed from the repository, you need to take care which version you are running. If you typed `nwipe` from any directory it will always run the original repository copy of nwipe. To run the latest nwipe you have to explicitly tell it where the new copy is, e.g in the directory `~/nwipe_master/nwipe/src` . That's why you would run it by typing `sudo ~/nwipe_master/nwipe/src/nwipe` alternatively you could cd to the directory and run it like this:

```
cd ~/nwipe_master/nwipe/src
./nwipe
```

Note the ./, that means only look in the current directory for nwipe. if you forgot to type ./ the computer would run the older repository version of nwipe.

Once you have copied the script below into a file called  buildnwipe, you need to give the file execute permissions `chmod +x buildnwipe` before you can run it. [Download script](
https://drive.google.com/file/d/1-kKgvCvKYYuSH_UUBKrLwsI0Xf7fckGg/view?usp=sharing)
```
#!/bin/bash
cd "$HOME"
nwipe_directory="nwipe_master"
mkdir $nwipe_directory
cd $nwipe_directory
sudo apt install build-essential pkg-config automake libncurses5-dev autotools-dev libparted-dev dmidecode readlink smartmontools git
rm -rf nwipe
git clone https://github.com/martijnvanbrummelen/nwipe.git
cd "nwipe"
./init.sh
./configure
make
cd "src"
sudo ./nwipe
```
### Quick & Easy, bootable version of Nwipe Master for x86_64 systems.
If you want to just try out a bootable version of nwipe you can download the ShredOS image [shredos-20200405.img](https://bit.ly/2XcaK3I) with the latest nwipe master and burn it to a USB stick

Check that the checksum matches
```
sha1sum shredos-20200405.img
73d9ea782cdf5cf213c11a1993995f0ee03b5878  shredos-20200405.img
```

Linux: Simply write it to a USB memory stick using the following command, replacing xxx with the device name of the USB memory stick you want to burn to.
```
dd if=shredos-20200405.img of=/dev/xxx
```
Windows: Use [Rufus](https://rufus.ie/), dd for windows or any of the many equivalents to burn the image to a USB stick.

Reboot your computer, making sure your bios is setup to boot from USB. ShredOS will boot very fast even on an older machine. On an old dual core system I use for testing, ShredOS boots in around 9 seconds straight into the Nwipe drive selection screen.

#### Some things to note:
- ShredOS has two tty terminals, ALT-F1 and ALT-F2. The default terminal ALT-F1 only runs nwipe. The version of nwipe that runs in the default terminal will automatically restart when you exit it, either at the end of a wipe or using CONTROL-C to abort. So if you want to run nwipe in the traditional way, along with any command line options you require, then use the second terminal ALT-F2, as an example, you could then use the command ```nwipe --nousb --logfile=nwipe.log``` etc. If you do use ALT-F2 to run a second copy of nwipe, please remember that if you already have one copy of nwipe already wiping the second copy of nwipe will hang on starting, therefore nwipe in the default terminal should be left at the drive selection screen to prevent the second occurence of nwipe from hanging. Alternatively, a second occurrence of nwipe could be started by specifying the drive on the command line, i.e.```nwipe /dev/sdc``` etc.
- The 20200405 version of ShredOS now includes the smartmontools package, Nwipes ability to detect serial numbers on USB devices now works on USB bridges who's chipset supports that functionality. This also now works in ShredOS 20200405.
- hdparm is also available for those that want to do a firmware supported wipe. A firmware wipe is a planned enhancement to nwipe.

## Bugs

Bugs can be reported on GitHub:
https://github.com/martijnvanbrummelen/nwipe

## License

GNU General Public License v2.0
