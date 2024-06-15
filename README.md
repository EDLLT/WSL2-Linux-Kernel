# What is this fork about?
This is the WSL2-Linux-Kernel that has been modified to enable loadable modules support and be able to use wireless adapters

# Video Tutorial
Amazing tutorial by TheConsciousCat on this whole process

https://www.youtube.com/watch?v=l7VQQJdePlk

# How to get this kernel

Make a directory named src  
`mkdir ~/src`

Clone the kernel
`git clone https://github.com/EDLLT/WSL2-Linux-Kernel.git`

# How do I add a wireless adapter driver and enable it?

Put your driver inside 
`drivers/net/wireless/realtek`

2. Edit the Kconfig file inside ``drivers/net/wireless/realtek/Kconfig`` and make it point to your driver just like this commit below
https://github.com/EDLLT/WSL2-Linux-Kernel/commit/e23de6383dcc5af1c2a50115029a9d318a63a997

3. Also edit the Makefile in ``drivers/net/wireless/realtek/Makefile`` and add the obj pointing to your driver just like below
https://github.com/EDLLT/WSL2-Linux-Kernel/commit/2b0edd829739af7b6f601a54ba815c67b1d564ef

4. Get into menuconfig
`make -j $(expr $(nproc) - 1) KCONFIG_CONFIG=Microsoft/config-wsl menuconfig`

5. Inside menuconfig goto
   `Device Drivers` (Hit Arrow down 15 times)
   `Network device support` (Hit arrow down 23 times)
   `Wireless LAN` (Hit arrow down 45 times)
   
6. Now that you are in Wirelass LAN go down up until you find `Realtek devices`(~55 downs)
& Find the driver you want to enable, in my case the driver I want to enable is called Realtek 8822B USB WiFi
Press `M` to enable it, (MAKE SURE IT SAYS `M` NOT `*` FOR THE DRIVER YOU WANT OTHERWISE IT MIGHT ERROR)

7. Save the configuration file as `Microsoft/config-wsl`

Now once you've done everything proceed to the below `New Build Instructions`
   
   


# New Build Instructions
1. Install the build dependencies:  
   `sudo apt install build-essential flex bison dwarves libssl-dev libelf-dev`

2. Load modules
   `make -j $(expr $(nproc) - 1) KCONFIG_CONFIG=Microsoft/config-wsl modules`

3. modules_install
   `make -j $(expr $(nproc) - 1) KCONFIG_CONFIG=Microsoft/config-wsl modules_install`

4. Build the kernel
   `make -j $(expr $(nproc) - 1) KCONFIG_CONFIG=Microsoft/config-wsl`

Assuming that there were no errors you're safe to proceed

5. A file called bzImage file will be created in `~/src/WSL2-Linux-Kernel/arch/x86/boot` copy the bzImage to /mnt/Users/YOUR_USER
   Note: Replace `YOURUSER` with your actual user

   `cp ~/src/WSL2-Linux-Kernel/arch/x86/boot/bzImage /mnt/c/Users/YOURUSER`

6. Create a file called .wslconfig inside `/mnt/c/Users/YOURUSER` and edit it
   nano /mnt/c/Users/YOURUSER/.wslconfig

7. Paste this in(Don't forget to replace YOURUSER with your actual user) then CTRL+X to save the file and hit enter
   ```
   [wsl2]
   kernel=C:\\Users\\USERNAME\\bzImageMODULESLOADED
   ```
8. goto windows command prompt and shutdown wsl(then wait for 10 seconds)
   `wsl --shutdown`

Now try typing wsl, give it a second or two and it should work, (If it doesn't and you want to revert then delete the .wslconfig inside C:\Users\YOURUSER)

# How to load the module for the driver

Goto your kernel directory
`cd /lib/modules/$(uname -r)/kernel/drivers/net/wireless/realtek`

Use ls to list out the folders available 
`ls`

My result
```
rtl88x2bu  rtl8xxxu
```

Once found cd into the folder containing the module you want to load in here I want to load rtl88x2bu
`cd rtl88x2bu`

Use ls again and find the file name then type modprobe NAME without `.ko`
`ls`

Result
```
88x2bu.ko
```

`sudo modprobe 88x2bu`

If everything works fine no error should pop up

use lsmod to list the loaded modules

`lsmod`

Result
```
Module                  Size  Used by
88x2bu               4104192  0
cfg80211             1093632  1 88x2bu
```

And that's it, now attach your usb using usbipd(https://docs.microsoft.com/en-us/windows/wsl/connect-usb#attach-a-usb-device)

after that running iwconfig gives me
`iwconfig`

Result
```
lo        no wireless extensions.

bond0     no wireless extensions.

dummy0    no wireless extensions.

eth0      no wireless extensions.

tunl0     no wireless extensions.

sit0      no wireless extensions.

wlan0     unassociated  Nickname:"WIFI@RTL88X2BU"
          Mode:Managed  Frequency=2.412 GHz  Access Point: Not-Associated
          Sensitivity:0/0
          Retry:off   RTS thr:off   Fragment thr:off
          Encryption key:off
          Power Management:off
          Link Quality:0  Signal level:0  Noise level:0
          Rx invalid nwid:0  Rx invalid crypt:0  Rx invalid frag:0
          Tx excessive retries:0  Invalid misc:0   Missed beacon:0
```

And that's it, you can do whatever you want with it as if you were in a normal linux environment!

If you didn't understand something then feel free to make an issue and i'll try to help whenever I can

The rest of the things below is from https://github.com/microsoft/WSL2-Linux-Kernel, didn't remove it just in case someone wanted it for some reason








# Introduction

The [WSL2-Linux-Kernel][wsl2-kernel] repo contains the kernel source code and
configuration files for the [WSL2][about-wsl2] kernel.

# Reporting Bugs

If you discover an issue relating to WSL or the WSL2 kernel, please report it on
the [WSL GitHub project][wsl-issue]. It is not possible to report issues on the
[WSL2-Linux-Kernel][wsl2-kernel] project.

If you're able to determine that the bug is present in the upstream Linux
kernel, you may want to work directly with the upstream developers. Please note
that there are separate processes for reporting a [normal bug][normal-bug] and
a [security bug][security-bug].

# Feature Requests

Is there a missing feature that you'd like to see? Please request it on the
[WSL GitHub project][wsl-issue].

If you're able and interested in contributing kernel code for your feature
request, we encourage you to [submit the change upstream][submit-patch].

# Build Instructions

Instructions for building an x86_64 WSL2 kernel with an Ubuntu distribution are
as follows:

1. Install the build dependencies:  
   `$ sudo apt install build-essential flex bison dwarves libssl-dev libelf-dev`
2. Build the kernel using the WSL2 kernel configuration:  
   `$ make KCONFIG_CONFIG=Microsoft/config-wsl`

# Install Instructions

Please see the documentation on the [.wslconfig configuration
file][install-inst] for information on using a custom built kernel.

[wsl2-kernel]:  https://github.com/microsoft/WSL2-Linux-Kernel
[about-wsl2]:   https://docs.microsoft.com/en-us/windows/wsl/about#what-is-wsl-2
[wsl-issue]:    https://github.com/microsoft/WSL/issues/new/choose
[normal-bug]:   https://www.kernel.org/doc/html/latest/admin-guide/bug-hunting.html#reporting-the-bug
[security-bug]: https://www.kernel.org/doc/html/latest/admin-guide/security-bugs.html
[submit-patch]: https://www.kernel.org/doc/html/latest/process/submitting-patches.html
[install-inst]: https://docs.microsoft.com/en-us/windows/wsl/wsl-config#configure-global-options-with-wslconfig
