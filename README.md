# Lenovo Ideapad 310 - 15ISK

> Hello and welcome to this guide about making lenovo ip310 laptop triple boot and make it ready for coding and developing (some parts are optional for you, like installing specific TextEditor or Package manager or etc)

## Requirements

> All needed files for this guide are in folders named `Windows`, `Linux`, `Mac`

-   OS `.iso`, `.dmg` file linkes are in `./{OS}/os.link`
-   Back up your important files in a safe place

## Bootable USB's

-   First download all OS'es from `./{OS}/os.link`

### Windows Bootable USB

> You need a Windows OS for making bootable flash using `Rufus` (or Linux, or Mac)

1. Open Rufus
2. From Device, select you USB device
3. Select Windows `.iso` file
4. Set partition scheme to `GPT`
5. Set target system to `UEFI`
6. Click Start

### Linux Bootable USB

> You need a Windows OS for making bootable flash using `Rufus` (or Linux, or Mac)

1. Open Rufus
2. From Device, select you USB device
3. Select Linux `.iso` file
4. Set partition scheme to `GPT`
5. Set target system to `UEFI`
6. Click `Start`

### Mac Bootable USB

> You need a Mac OS for making bootable flash using `Unibeast` (or Windows `TransMac`, or Linux `dmg2img + dd + mkfs` )

-   You can run macOS from `VMWare` and make your bootable flash if you haven't any macOS

1. Open `Disk Utility` application from `Launchpad/Other/Disk Utility`
2. Click you USB device and click `Erase`
3. Set to `GUID` + `JHFS+`
4. Format USB device
5. Open Mac `.dmg` file
6. Copy `Mac Installer` to `Applications` folder
7. Open `Unibeast`
8. Goto `Destination Select` and select your USB device
9. Goto `Installation` and click `Install`
10. After installing completes, open `CloverConfigurer`
11. Goto `Mount EFI` section and mount your USB device EFI partition
12. Click `Open Partition` and goto `EFI/CLOVER/config.plist`, double click to open file with `CloverConfigurer`
13. Goto `Graphics` section and check `Inject Intel`, and set `ig-platform-id` to `0x12345678` (Fake ID)
14. Goto `SMBIOS` sectopms and set `Memory Channels` combo box to `Dual Channel`, and set `SlotCount` to `1`
15. Click `Save`
16. Unmount your USB device EFI partition
17. Exit `CloverConfigurer`

## Installing

> Now it's time to going to installing OS'es, first Windows and then Linux and then Mac

-   Connect you USB device and boot it from `UEFI` mode

### Windows 10 Installation

1. Open `CMD` and run `diskpart` and do these things
    ```code
    list disk
    select disk <hard disk number>
    clean
    convert gpt
    exit
    ```
2. Open installer partitioner and create `300GB` partition and format for Windows
3. Back to `CMD` and rerun `diskpart` and do these things
    1. Remove `Windows Recovery` partition and `ESP (EFI Partition)`
    2. Create new `ESP (EFI Partition)` with size of 600MB
    3. Back to installer partitioner
4. Install Windows

### GNU/Linux Installation

1. Goto paritioner
2. Create 4GB swap
3. Create 396GB root
4. Set grub installation to `/dev/sda` (Hard drive)
5. Install Linux
6. Boot installed linux (For adding `macOS Clover Bootloader` chainloader item to `GRUB2`)
7. Open terminal and do these things
    1. Open `/etc/grub.d/40_custom` with TextEditor (or lovely `VI`) :D
    2. Add these lines at the end of file
        ```code
        menuentry 'macOS Sierra' {
            insmod fat
            insmod part_gpt
            insmod search_fs_uuid
            search --file --no-floppy --set=root /EFI/CLOVER/CLOVERX64.efi
            chainloader /EFI/CLOVER/CLOVERX64.efi
        }
        ```
8. Run `sudo update-grub`

### macOS Sierra 10.12.6 Installation

1. Open `Disk Utility`
2. Format 300GB partition as `JHFS+` format
3. Install macOS on new partition
4. Don't eject USB device
5. Reboot after installation
6. Boot flash `Clover Bootloader` and then boot `macOS` installed from that
7. Complete installation
8. Open `Multibeast` and check these items
    1. `Drivers/Audio/Universal/VoodooHDA v2.8.9`
    2. `Drivers/Disk/Intel Generaic AHCI SATA`
    3. `Drivers/Misc/*`
    4. `Drivers/Misc/VoodooTSync 4 Core`
    5. `Drivers/Network/Atheros/AtherosE2200Ethernet v2.2.1`
    6. `Drivers/USB/3rd Party USB 3.0`
    7. `Bootloader/Clover v2.4 * UEFI + Emulated NVRAM`
    8. `Customize/Graphics Configuration/Intel HD 5xx`
    9. `Customize/Graphics Configuration/Nvidia Web Drivers Boot Flag`
9. From Build menu click Install
10. Now Open `Clover Configurer` and do these items
    1. Goto `Mount EFI` section and mount your Hard device EFI partition
    2. Click `Open Partition` and goto `EFI/CLOVER/config.plist`, double click to open file with `CloverConfigurer`
        1. Goto `ACPI` and check `ADDPNLF_*` (brightness fix)
        2. Goto `Boot` and check `nv_disable=1` (graphics fix)
        3. Goto `Graphics` and check `Inject Intel` (brightness fix)
        4. Goto `Graphics` and set `ig-platform-id` to `0x19160000` (Real ID Intel HD 520)
        5. Goto `Kernel and Kext Patches` and add these to `KextToPatch` as new item (graphics fix)
            1. Name: `com.apple.driver.AppleIntelSKLGraphicsFramebuffer`
            2. Find: `8945c839 c67651`
            3. Replace: `8945c839 c6eb51`
            4. Comment: `anything`
            5. MatchOS: `10.12.x`
        6. Goto `Rt Variables` and set `BooterConfig` to `0x28`
        7. Goto `Rt Variables` and set `CsrActiveConfig` to `0x3`
        8. Goto `SMBIOS` and check `Mobile`
        9. Goto `SMBIOS` and check `Trust`
        10. Goto `SMBIOS` and add these to `Memory` as new item (8G RAM fix)
            1. Slot: `0`
            2. Size: `8192`
            3. Frequency: `2133`
            4. Vendor: `HyperX`
            5. Part: `13A1E908`
            6. Serial: `RMSA3230KE68H9F213`
            7. Type: `DDR4`
        11. Goto `SMBIOS` and set `Channels` to `Dual Channel`
        12. Goto `SMBIOS` and set `SlotCount` to `1`
    3. Click `Save`
    4. Copy all `.kext` files (in `Mac/kexts` folder) to `/EFI/CLOVER/kexts`
    5. Unmount your USB device EFI partition
11. Run `sudo kextcache -i /` (remove link file if error 17 shows)

**TODO**: Battery status

## Customizing

### Windows 10 Customizing

#### Drivers

1. Install Lenovo Drivers

#### PackageManager & Proxy
    
1. Install Chocolaty package manager
2. Install Ultrasurf(9050)
3. Run `choco install privoxy` => config 9050 socks5t
4. Edit file `privoxy/config.txt`
    ```code
    forward-socks5t / 127.0.0.1:9050 .
    ```

#### Install

1. Run 
    ```code
    choco install ^
    steam winrar vscode uget mpv firefox googlechrome git conemu cmake nvm Bitnami-XAMPP ^
    --installargs '"ADD_CMAKE_TO_PATH=System"' ^
    --proxy=127.0.0.1:9050
    ```
2. Install MinGW, Java-JDK-8
3. Install Microsoft Office, Adobe XD, Telegram
4. Install VisualStudio, CLion, Rider, DotPeek, Qt

#### Config

1. Config autostart, quake `conemu`
2. Config VSCode settings sync(token + gistid)

#### Screenshots

![Shot 1](https://github.com/koliberr136a1/ip310-tripleboot/blob/master/Windows/Screenshot%20(3).png)
![Shot 2](https://github.com/koliberr136a1/ip310-tripleboot/blob/master/Windows/Screenshot%20(4).png)

### GNU/Linux Customizing

#### Drivers

1. Run `sudo ubuntu-drivers autoinstall`

#### PackageManager & Proxy

1. Run `chmod 0777 /opt`
2. Change Repository to Main Servers
3. Run `sudo apt install tor privoxy`
4. Edit file `/etc/systemd/system/multi-user.target.wants/tor.service`
    ```code
    [Unit]
    Description=Anonymizing overlay network for TCP (multi-instance-master)

    [Service]
    User=debian-tor
    Type=simple
    RemainAfterExit=yes
    ExecStart=/usr/bin/tor -f /etc/tor/torrc
    ExecReload=/usr/bin/kill -HUP $MAINPID
    KillSignal=SIGINT
    LimitNOFILE=8192
    PrivateDevices=yes

    [Install]
    WantedBy=multi-user.target
    ```
5. Edit file `/etc/privoxy/config`
    ```code
    forward-socks5t / 127.0.0.1:9050 .
    ```
6. Run
    ```code
    sudo systemctl daemon-reload
    sudo systemctl restart tor.service
    sudo systemctl restart polipo.service
    ```
7. Control tor, privoxy services using these commands:
    ```code
    sudo service {tor|privoxy} {start|stop}
    ```

#### Install

1. Run
    ```code
    sudo torsocks apt-add-repository ppa:fixnix/netspeed
    sudo torsocks apt-add-repository ppa:tista/adapta
    sudo torsocks apt-add-repository ppa:papirus/papirus

    sudo curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash

    sudo curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo install -o root -g root -m 644 microsoft.gpg /etc/apt/trusted.gpg.d/
    sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list'

    sudo torsocks apt update
    sudo torsocks apt upgrade

    nvm install node

    sudo torsocks apt install \
    steam unrar code uget mpv firefox chromium-browser git guake docker.io gcc clang cmake erlang openjdk-8-jdk lamp-server^ \
    audacious redshift \
    apt-transport-https ca-certificates curl software-properties-common \
    indicator-multiload adapta-gtk-theme papirus-icon-theme

    git clone --depth=1 https://github.com/Bash-it/bash-it.git ~/.bash_it
    ~/.bash_it/install.sh
    # edit .bashrc => change Theme
    ```
2. Install Libre Office, Telegram
3. Install CLion, AndroidStudio, Qt

#### Config

1. Config desktop theme (`papirus`, `adapta`)
2. Config autostart `guake`
3. Config desktop panel
4. Config VSCode settings sync(token + gistid)

### macOS Sierra 10.12.6 Customizing

#### Drivers
    
1. Install `HoRNDIS-9.2.pkg` for USB tethering
2. Install `CustoMac Essentials.pkg` for Ethernet

#### PackageManager & Proxy

1. Install Homebrew package manager
2. Run `brew install tor privoxy`
3. Edit file `/usr/local/etc/privoxy/config`
    ```code
    forward-socks5t / 127.0.0.1:9050 .
    ```
4. Control tor, privoxy services using these commands:
    ```code
    brew services list
    brew services {start|stop} {tor|privoxy}
    ```

#### Install

1. Run
    ```code
    brew install \
    unrar git clang cmake erlang \
    
    brew cask install \
    steam visual-studio-code mpv firefox google-chrome iterm2 \

    git clone --depth=1 https://github.com/Bash-it/bash-it.git ~/.bash_it
    ~/.bash_it/install.sh
    # edit .bashrc => change Theme
    ```
2. Install Clang, Java-JDK-8, Mamp Server
3. Install Telegram Adobe XD
4. Install CLion, XCode

#### Config

1. Config autostart, quake `iterm2`
2. Config VSCode settings sync(token + gistid)
3. Config map keyboard(`command`->`control`, `control`->`command`)

### VSCode Installation (Use with `redshift` at nights :D)

1. Install VSCode
2. Install `texlive-full`, `npm`, `node`
3. Install `Settings Sync` plugin (gist) and sync settings and plugins
4. Set keybinding (Integrated Terminal) based on OS type (Ctrl+Shift+T | Ctrl+Alt+T)
