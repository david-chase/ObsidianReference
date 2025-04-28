#swap #linux #k8s 

## **Turn off swap immediately (temporary):**

`sudo swapoff -a`

## **Prevent swap from coming back at reboot:**

Edit `/etc/fstab`:

`sudo nano /etc/fstab`

Find the line that looks like:

`/swap.img swap swap defaults 0 0`

and comment it out.