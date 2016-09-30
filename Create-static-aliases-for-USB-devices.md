
1. Find out `/dev/ttyWHAT`
2. `sudo udevadm info --name=/dev/ttyWHAT--attribute-walk`
3. edit `/etc/udev/rules.d/90-usb-dagor.rules` (`sudo vi ...`)
4. add a line like this:

    SUBSYSTEM=="tty", ATTRS{idVendor}=="2341", ATTRS{idProduct}=="0042", ATTRS{serial}=="853323433323516040B0", SYMLINK+="dagor_dome"

5. reset USB subsystem: `sudo udevadm trigger`

However...

Cheapo USB chips don't have serial numbers o_O. And of course, have all other attributes the same. so we are only left with physical ports as the distinguishing element.

In that case:

    KERNEL=="ttyUSB*", KERNELS=="2-1.3:1.0", NAME="dagor_fans"


http://unix.stackexchange.com/a/183492/50005

http://askubuntu.com/questions/49910/how-to-distinguish-between-identical-usb-to-serial-adapters