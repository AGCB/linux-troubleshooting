I had trouble connecting to wifi and reached out to the Manjaro Linux Forum for help.
The fix ended up being a button I didn't know I accidentally pressed on the side of my
laptop! Either way I learned a bit about linux as the community talked me through possibilities.
I tried usbBooting 3 different distros before I figured out the problem.
The pipeline for using Rufus to make an ISO into a bootableUSB on my windows machine is easy now.
Not that I have a reason for leaving Manjaro yet, but still, very cool.
Here is the full thread....
AND
For next Time... rfkill list..... don't forget to check that the wifi isn't hard blocked!!...



------------------------

Hello Manjarians,

I dont see any network connections anymore . Ive used Manjaro for years and never had this problem.
What steps should i use to troubleshoot?

Are there security concerns with posting the output of commands like “inxi -Fx” or “ifconfig” 
in full when reaching out in public for help?
Thanks

PhrosGone
Hi
No, with inxi we can see only your hardware information. If you don’t work for a hardware vendor 
and use prototype hardware, there should be no concern at all! :wink:
Same for ifconfig. We can see your local IP. This is nothing you should be concern about.

Photon
I don’t know how to solve this issue, but you might find some information and/or assistance 
in this thread: Network Manager MegaThread2

dustinSagona
Thank you

stephane
this is a IvyBridge with Intel 82579LM
since release kernel 4.14.3 and 4.9.68
there is a bug to get NIC with some cards ( i218/i219-V and i218/i219-LM and 82xxxLM series) in driver e1000e
try to see with others kernels:
launch your USB install key
open a terminal
manjaro-chroot -a
mhwd-kernel -i linux413
mhwd-kernel -i linux415
exit
then reboot
you can choose another kernel on grub start

dustinSagona
Rightclicking the network cpnnections icon on the menu bar shows a checkbox for 
“Enable wifi” that i cannot check. Clicking simply closes the window and upon 
reopening i see that it is still unchecked

mleman
Well you might suffer from the issue I described. I can see in your screenshot that your network card got no IP.
To verify, let’s check which version of networkmanager you have installed:
pacman -Q networkmanager
If it says something with 1.10+, you are on the problematic new version. If it is 1.8 or less, 
the following fix will not be for you.
Now check logs with
journalctl -u NetworkManager|grep dhcp4|grep timeout
You might see something like
NetworkManager[457]: <info>  [1512328498.8984] dhcp4 (wlp2s0): activation: beginning transaction (timeout in 45 seconds)
NetworkManager[457]: <info>  [1512328544.0574] dhcp4 (wlp2s0): state changed unknown -> timeout
NetworkManager[457]: <info>  [1512328544.0794] dhcp4 (wlp2s0): state changed timeout -> done
If this is the case, then DHCP has been unable to obtain an IP address which is neccessary for networking.
You can try the fix I described here.
For your convenience, this one-liner will create the file as needed:
sudo bash -c "{ echo '[main]'; echo 'dhcp=dhclient'; }|cat >/etc/NetworkManager/conf.d/dhclient.conf"
Reboot and check if you are online again. If not, you do not suffer from this problem but from another. 
To revert my fix, 
simply delete the previously created file with
sudo rm /etc/NetworkManager/conf.d/dhclient.conf

dustinSagona
Also i have confirmed on an older machine that my current location’s wifi IS working

dustinSagona
My networkmanager version is 1.10.2-1
The command line fix you suggested didnt work. I tried a reboot twice.
Any other suggestions?

jonothon
Please don’t cross post. You already have an open thread.
Moving these posts.

Photon
I’m sorry, that was my idea (I thought the Megathread was supposed to contain all Network Manager related issues, it’s not?).

jonothon
To a point - but there’s no need to re-post the entire thing in another thread. Just a link would have done.

dustinSagona
Understood jonathon thank you. Ill link next time

jonothon
No worries.
I think @mleman has posted what could potentially be the solution above:
Have a read through that and see if it helps.

   there is a bug to get NIC with some cards ( i218/i219-V and i218/i219-LM and 82xxxLM series) in driver e1000e

Yes.
https://sourceforge.net/p/e1000/bugs/588/2


Stephane,
Running the mhwd commands you suggested returned errors.
The last 3 lines being…
warning: failed to retreive some files
Error: failed to commit transaction (download library error)
Errors occurred, no packages were upgraded.

What do you suggest?
I could post a bit of the output but both commands returned return lengthy outputs.

Any other suggestions?
I see that the bug was reported on Dec11th.

Is it relevant at all that I haven’t had problems until yesterday? 
The machine has always been connected to 1 location’s wifi. Yesterday I 
connected the machine to 2 different coffee shop’s wifi for the first time.

Change kernels help at all?

what return

mhwd-kernel -li  ( list all kernel installed )

Linux49

No others are listed.

you get errors on this command ( from usb stick intall ) ?
manjaro-chroot -a

This output shows you’re getting a timeout while trying to receive an IP address via 
DHCP which is pretty much exactly the issue that @mleman’s solution will fix.
Can you please verify the content of /etc/NetworkManager/conf.d/dhclient.conf, and that you have dhclient installed?

$ cat /etc/NetworkManager/conf.d/dhclient.conf
[main]
dhcp=dhclient
$ dhclient --version
isc-dhclient-4.3.6
I restarted the machine and still don’t see any potential networks in the menu bar.
My error occurs not on the manjaro-chroot -a, but instead on the 2 mhwd commands.


Thank you Jonathan, for reposting mleman’s suggestions
sudo bash -c “{ echo ‘[main]’; echo ‘dhcp=dhclient’; }|cat >/etc/NetworkManager/conf.d/dhclient.conf”
On this machine which hadn’t been booted in a long time, My ethernet connection died after 
upgrading to networkmanager 1.10.2-1 That one-liner creating a dhclient.conf file fixed my trouble. 
Sending mleman a note of thanks, as well.

Thanks for everything Manjaronians.
I tried booting Mint and Ubuntu from USB and had the same issue.
I finally found my solution on Ubuntu docs…
"
If your wireless card is inside your computer, make sure that the wireless switch is turned on (if it has one). "…
Sorry to have used everyone’s time for something so silly. 
My laptop has a hard switch on the side which toggles wifi ON/OFF…
I must have accidentally hit the switch yesterday.
For future reference, are there terminal commands that y’all would have ran to get 
to this conclusion quicker than I did?

Yes, that would be
rfkill list
and you would have seen that your wifi is hardBlocked

Nice. Thanks @Photon

