# Installation openSUSE Leap 16 Beta on HP Elitebook 840 G11 (Dualboot)

I spend some time, to get openSUSE Leap 16 up and running on my new HP Elitebook 840 G11 with my Dell U3425we Hub-Monitor.
<br><br>
Here are some hints for the installation:
<br>

- Reduce the Windows Partion to get space for Linux<br>
    During the installation, I have not found a way to reduce the size of the Windows partition. Therefore I created the partition under Windows with Disk Management (diskmgmt.msc)
    Then you can choose the free partion during the installation via the Agama-Installer <br>
    <img src="AgamaPartions.jpg" width="400" > <br>
    I installed openSUSE on nvme0n1p4 with action "Delete" and not "allow shrink"

<br>

-   Install from USB stick<br>
    
    To get into the BIOS, press ESC serveral times during startup

    Then choose "Boot Menu (F9)" from the BIOS menu.
    There choos your USB stick to install openSUSE

    I had to disable secure boot in the BIOS.

-   WiFi works out of the box :smile:
<br>

-   Audio / Sound<br>
    Does not  work out of the box<br>
    Install "sof-firmware"
<br>

-   Mount Windows Partion<br>
   If you see a yellow warning triangle on your windows drive like:<br>

   <img src="BitlockerWarnung.PNG" width="200" > <br>
   Then your Bitlocker encryption is not complete. Just complete your Bitlocker encryption [^1]. Now you cann access your Windows drive via Dolphin. You have to enter two passwords. The first one is the Bitlocker-Key and the second one is your password. <br>If you do not need a Bitlocker encryption on your Windows Drive, you can decativate your Bitlocker encryption after the successfull encryption.


-   Problems with Dockingstation (Dell Monitor U3425we)<br>

    *   Change Bios Settings

        +   Disable FastBoot:<br>
        After disabling FastBoot, I can use mouse and keyboard on the GRUB screen.
        Also the monitor speakers works after disabling FastBoot.

        + Boot on Power:<br>
        If you want, that the Laptop boots, when you connect Thunderbold (Boot on Power)
        Disable "HP Powermangement" and enable "Boot on Power" in the BIOS
        
    *   Change GRUB2 stetting
        +   During the start up, no login screen after booting.<br>
             Therefore, I had to enforce to usse the montior screen.

             First, get your display via<br>
            ```
            xrandr --listmonitors 
            ```


            I get:<br> 
            ```
            0: +*eDP-1 2560/301x1600/188+0+0  eDP-1
            1: +DP-3 640/169x480/127+2560+0  DP-3
            ```
            DP-3 is my Dell monitor. With ":D" I force to use this monitor.<br>
            Extend GRUB_CMDLINE_LINUX with "video=DP-3:D video=eDP:e" in /etc/default/grub and generate the new grub config [^2] <br>
            ```
            GRUB_CMDLINE_LINUX_DEFAULT="mitigations=auto quiet video=DP-3:D video=eDP-1:e security=selinux selinux=1"
            ```


    *   Still open:<br>
        +    Login screen lauches only with the resolution of 640x480. But after login, I have my preferd resultion.

[^1]: https://itler.net/laufwerk-mit-orangen-warndreieck-und-ausrufezeichen/

[^2]: https://superuser.com/questions/1583625/display-grub-on-external-monitor-with-docking-station
