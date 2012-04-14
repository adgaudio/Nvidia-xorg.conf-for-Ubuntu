There's a lot of stuff on the web about how to setup your nvidia card with ubuntu.  After a lot of work, I am able to switch between single and dual monitor setup with an nVidia Quadro 1000M video card on my Lenovo Thinkpad w520 running Ubuntu 11.10.  The following is an instruction list that worked for me.


BIOS: set to discrete and disable optimus detection

Backup: cp /etc/X11/xorg.conf /etc/X11/xorg.conf.original

# OPTIONAL?  I did this, but not sure if any of it is necessary

    sudo add-apt-repository ppa:ubuntu-x-swat/x-updates
    apt-get --purge remove ALL_THINGS_NVIDIA
    apt-get install --reinstall nvidia-173 nvidia-173-updates nvidia-settings
    apt-get install --reinstall  nvidia-current nvidia-current-updates # necessary?

Run "sudo nvidia-xconfig" to replace the current xorg.conf file.

Edit /etc/X11/xorg.conf and add the following to "Device" section

    BusId "PCI:1:0:0"
    Option         "RegistryDwords" "EnableBrightnessControl=1"
    Option         "NoLogo" "True"

    Where the BusId bit is determined by the output of:
    $ lspci | grep -i nvidia | grep -i VGA | cut -d " " -f 1

Add the following to /etc/modprobe.d/blacklist.conf

    blacklist vga16fb
    blacklist nouveau
    blacklist rivafb
    blacklist nvidiafb
    blacklist rivatv

Reboot, and if everything works, I highly recommend saving the xorg.conf file somewhere (like in a git repo)


If you want dual monitors, run "nvidia-settings" and click the "detect monitors" button.  Don't "save" the settings because you'll probably overwrite the xorg.conf file that has your customizations in it.

If you get a black screen on reboot and want to try hacking around, don't forget about these:
- Hitting <CTRL><ALT><F1> to login to a terminal without X
- Checkout man pages for xrandr, startx and X (though these may not work properly with the nvidia drivers)

If your hosed and want to undo everything, change the BIOS back to its original settings (probably Integrated Graphics) and replace (or remove) the xorg.conf file

    $ cp /etc/X11/xorg.conf.original /etc/X11/xorg.conf
    ## Don't forget to unblacklist the stuff in blacklist.conf! ##
    $ sudo reboot
    ## Did you change the BIOS?
