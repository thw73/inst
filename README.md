# Installation openSUSE Leap 16 Beta on HP Elitebook 840 G11 (Dualboot)

I spend some time, to get openSUSE Leap 16 up and running on my new HP Elitebook 840 G11 with my Dell U3425we Hub-Monitor.
<br><br>
Here are some hints for the installation:
<br>

1. Reduce the Windows Partion to get space for Linux
> During the installation, I have not found a way to reduce the size of the Windows partition. Therefore I created the partition under Windows with Disk Management (diskmgmt.msc)
Then you can choose the free partion during the installation via the Agama-Installer    
<p>
&emsp;<img src="AgamaPartions.jpg" width="400" >
</p>
I installed openSUSE on nvme0n1p4 with action "Delete" and not "allow shrink"
<br>
2. Install from USB stick
    
    To get into the BIOS, press ESC serveral times during startup

    Then choose "Boot Menu (F9)" from the BIOS menu.
    There choos your USB stick to install openSUSE

    I had to disable secure boot in the BIOS.

3.  WiFi works out of the box :smile:
<br>

4. Audio / Sound
    Do not  works out of the box
    Install "sof-firmware"
<br>

5. Mount Windows Partion
   If you see a yellow warning triangle on your windows drive like:

   <p>
   &emsp;<img src="img/BitlockerWarnung.PNG" width="200" >
   </p>
   Then your Bitlocker encryption is not complete. Just complete your Bitlocker encryption [^1]
   Now you cann access your Windows drive via Dolphin. You have to enter two passwords. The first one is the Bitlocker-Key and the second one is your password. 
   If you do not need a Bitlocker encryption on your Windows Drive, you can decativate your Bitlocker encryption after the successfull encryption.

<br>
6. Problems with Dockingstation (Dell Monitor U3425we)

>>> 6.1 Change Bios Settings

>>> Disable FastBoot:
>>>      After disabling FastBoot, I can use mouse and keyboard on the GRUB screen.
>>>      Also the monitor speakers works after disabling FastBoot.

>>> Boot on Power:
>>> If you want, that the Laptop boots, when you connect Thunderbold (Boot on Power)
>>> Disable "HP Powermangement" and enable "Boot on Power" in the BIOS
        
>>> 6.2 Change GRUB2 stetting
>>>>During the start up, no login screen after booting. There, I had to enforce to use the montior screen.

>>>>First, get your display via
``` 
        xpandr
```  
>>>>I get: DP-3
<br>
>>>>Extend GRUB_CMDLINE_LINUX with "video=DP-3:D video=eDP:e" in /etc/default/grub and generate the new grub config [^2]
<br>
>>>>Still open: Login screen lauches only with the resolution of 640x480. But after login, I have my preferd resultion.

[^1]: https://itler.net/laufwerk-mit-orangen-warndreieck-und-ausrufezeichen/

[^2]: https://superuser.com/questions/1583625/display-grub-on-external-monitor-with-docking-station
