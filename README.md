*Migrated from https://github.com/Lekensteyn/acpi-stuff*

bbswitch is a kernel module which automatically detects the required ACPI calls
for two kinds of Optimus laptops. It has been verified to work with "real"
Optimus and "legacy" Optimus laptops (at least, that is how I call them). The
machines on which these tests has performed are:

- Clevo B7130 - GT 425M ("real" Optimus, Lekensteyns laptop)
- Dell Vostro 3500 - GT 310M ("legacy" Optimus, Samsagax' laptop)

(note: there is no need to add more supported laptops here as the universal
calls should work for every laptop model supporting either Optimus calls)

It's preferred over manually hacking with the acpi_call module because it can
detect the correct handle preceding _DSM and has some built-in safeguards:

- You're not allowed to disable a card if a driver (nouveau, nvidia) is loaded.
- Before suspend, the card is automatically enabled. When resuming, it's
  disabled again if that was the case before suspending. Hibernation should
  work, but it not tested.

To use it, build the module first (kernel headers are required):

    make
Then load it (requires root privileges, i.e. `sudo`):

    make load
If your card is supported, there should be no error. Otherwise, you get a "No
such device" (ENODEV) error. Check your kernel log (dmesg) for more
information.

Usage (`#` means "run with root privileges, i.e. run it prefixed with `sudo `):

Get the status:

    # cat /proc/acpi/bbswitch  
    0000:01:00.0 ON

Turn the card off, respectively on:

    # tee /proc/acpi/bbswitch <<<OFF
    # tee /proc/acpi/bbswitch <<<ON
If the card stays on when trying to disable it, you've probably forgotten to
unload the driver,

    $ dmesg |tail -1
    bbswitch: device 0000:01:00.0 is in use by driver 'nouveau', refusing OFF
This module has been integrated in Bumblebee "Tumbleweed". Please report any
issues on this module in the issue tracker and provide the following details:

- The output of `dmesg | grep -C 10 bbswitch:`
- The kernel version `uname -a`
- Your distribution and version (if applicable)
- The output of `lspci -d10de: -vvv`
- The version of your Xorg and the driver
