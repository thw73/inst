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
   <img src="BitlockerWarnung.PNG" width="200" ><br>
   Then your Bitlocker encryption is not complete. Just complete your Bitlocker encryption [^1]. Now you cann access your Windows drive via Dolphin. You have to enter two passwords. The first one is the Bitlocker-Key and the second one is your password. <br>If you do not need a Bitlocker encryption on your Windows Drive, you can decativate your Bitlocker encryption after the successfull encryption.


-   Problems with Dockingstation (Dell Monitor U3425we)<br>

    *   Change Bios Settings

        +   Disable FastBoot:<br>
        After disabling FastBoot, I can use mouse and keyboard on the GRUB screen.
        Also the monitor speakers works after disabling FastBoot.

        + Boot on Power:<br>
        If you want, that the Laptop boots, when you connect Thunderbold (Boot on Power)
        Disable "HP Powermangement" and enable "Boot on Power" in the BIOS [^3]

        
    *   Change GRUB2 stetting
        +   During the start up, no login screen after booting.<br>
             Therefore, I had to enforce to usse the montior screen.

             First, get your display via<br>
            ```console
            xrandr --listmonitors 
            ```


            I get:<br> 
            ```console
            0: +*eDP-1 2560/301x1600/188+0+0  eDP-1
            1: +DP-3 640/169x480/127+2560+0  DP-3
            ```
            DP-3 is my Dell monitor. With ":D" I force to use this monitor.<br>
            Extend GRUB_CMDLINE_LINUX_DEFAULT with "video=DP-3:D video=eDP:e" in /etc/default/grub and generate the new grub config [^2] <br>
            ```console
            GRUB_CMDLINE_LINUX_DEFAULT="mitigations=auto quiet video=DP-3:D video=eDP-1:e security=selinux selinux=1"
            ```


    *   Still open:<br>
        +    Login screen lauches only with the resolution of 640x480. But after login, I have my preferred resolution.


# Intel GPU driver for oneAPI

-   Install GPU driver<br>  
    If you have the supported hardware [^7], you can install oneAPI.<br>
    Here you find the information, how to install oneAPI [^4].
    
    Here is also a documentation, how to install the Client GPU driver for Ubuntu[^5] <br>
    In addition to this documentation, here are the related openSUSE Leap 16 Packages:


    
    | Ubuntu 2022<br>Package   | openSUSE Leap 16<br>Package | Comment     |
    |--------   |-------------|-----------|
    | libze-intel-gpu1      | libze_intel_gpu1           | -        |
    | libze1      | level-zero           | -        |
    | intel-opencl-icd        | intel-opencl           | -        |
    |  clinfo       | clinfo           | -        |

    <br>

    Verifying the installation<br>
    ````console
    sudo clinfo | grep "Device Name"
    ````
    you should get:<br>
    ````console
    Device Name                                   Intel(R) Arc(TM) Graphics 
    Device Name                                   Intel(R) Core(TM) Ultra 7 155H 
    Device Name                                   Intel(R) Arc(TM) Graphics 
    Device Name                                   Intel(R) Arc(TM) Graphics 
    Device Name                                   Intel(R) Arc(TM) Graphics
    ````

- Install oneAPI    

    Install oneAPI: see [^4] <br>

    ````console
    sudo zypper addrepo https://yum.repos.intel.com/oneapi oneAPI

    rpm --import https://yum.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB

    sudo zypper install intel-basekit
    ````
    oneAPI (version 2025.2) needs about 10GB disc space.


- Pytorch

    For Pytorch you need in addtion these following packages:
    | Ubuntu 2022<br>Package   | openSUSE Leap 16<br>Package | Comment     |
    |--------   |-------------|-----------|
    | libze-dev       | level-zero-devel           | -        |
    | python3-dev      | python313-devel          | Python.h        |
    <br>
    
    These packages are needed, if you want to use torch.compile [^8] [^9].<br>

    For Pytorch you need the Deep Learning Essentials from oneAPI [^10]<br>
    Check you oneAPI version and instal the corresponding version. In my case 2025.2

     ````console
    sudo zypper install intel-deep-learning-essentials-2025.2
    ````
    Then you can follow the instruction in [^11] and install pytorch

    ````console
    pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/xpu
    ````

# Intel NPU driver and OpenVINO™


- Install NPU driver <br>
    You can install the offical OpenSUSE driver "linux-npu-driver" via Myrlyn or zypper
    After the installation you have to add your user to the render group [^12]

    ````console
    sudo usermod -a -G render $USER 
    ````
   Reboot your system

  Check driver

   ````console
    /dev/accel> ls -la
    crw-rw----. 1 root render 261, 0 31. Dez 16:09 accel0
    ````

   ````console
    sudo dmesg | grep intel_vpu
   [drm] Initialized intel_vpu 1.0.0 for 0000:00:0b.0 on minor 0
    ````
   
- Install OpenVINO™  <br>
   
     ````console
   pip3  install "openvino=2025.2.0"
    ````
     
     - Install OpenVINO™ NPU plug <br>
         You can install the plugin "openvino-intel-npu-plugin" via Myrlyn or zypper

       Test installation
       
    ````console
    python3 -c "from openvino import Core; print(Core().available_devices)"

    ['CPU', 'GPU', 'NPU']
    ````
    
       
  
   

[^1]: https://itler.net/laufwerk-mit-orangen-warndreieck-und-ausrufezeichen/

[^2]: https://superuser.com/questions/1583625/display-grub-on-external-monitor-with-docking-station

[^3]: https://h30434.www3.hp.com/t5/Notebook-Software-and-How-To-Questions/EliteBook-wake-power-up-on-Thunderbolt-connection/m-p/8935372

[^4]: https://en.opensuse.org/SDB:Install_oneAPI

[^5]:https://dgpu-docs.intel.com/driver/client/overview.html#ubuntu-22.04

[^6]: https://docs.pytorch.org/docs/2.8/notes/get_start_xpu.html

[^7]: https://dgpu-docs.intel.com/devices/hardware-table.html

[^8]: https://pytorch.org/get-started/pytorch-2-x/

[^9]: https://docs.pytorch.org/tutorials/unstable/inductor_windows.html

[^10]: https://www.intel.com/content/www/us/en/developer/articles/tool/pytorch-prerequisites-for-intel-gpu/2-8.html

[^11]: https://docs.pytorch.org/docs/stable/notes/get_start_xpu.html

[^12]: https://medium.com/@cabelo_20098/installing-intel-npu-driver-on-linux-dde21924ec1f


[comment]: # (Package XY needs the Kernel Hearder, therefore you have to install the kernel header first)

