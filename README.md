# Lenovo ThinkPad X1 Carbon Gen 4 | OpenCore Configuation

macOS Version: **26** | **Tahoe**

OpenCore Version: **1.0.6** | **08-10-2025**

**This EFI is provided as-is and I will close any unrelated issues below the current official version that this EFI supports.**

![preview](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/blob/main/Resources/README/preview.png)

**10.10.2025:**<br/>
Initial Release<br/>

# Tested on

<details>  
<summary><strong>Hardware</strong></summary>
</br>

| Model            | Thinkpad X1 Carbon Gen 4                                                                                   |
| :--------------- | :--------------------------------------------------------------------------------------------------------- |
| Display          | 14" FHD 1920x1080 Non-Touch "Retina" IPS (Read post-install section)                                       |
| Processor        | Intel® Core™ i7-6500U                                                                                      |
| Graphics         | Intel® HD Graphics 520 (Spoofed as HD Graphics 620)                                                        |
| Memory           | 8GB Soldered 1866MHz DDR3, dual-channel                                                                    |
| Storage          | Intel 256GB NVMe SSD                                                                                       |
| WLAN + Bluetooth | 802.11ac + BT, Intel® Dual Band Wireless-AC 8260                                                           |
| Mobile Data      | Needs custom BIOS, hardware unknown & untested on macOS                                                    |
| Ethernet         | Intel Ethernet Connection I219-LM (Jacksonville)                                                           |
| Camera           | HD 720p resolution, low light sensitive, fixed focus                                                        |
| Audio support    | HD Audio, Conexant CX11852 codec, stereo speakers 1Wx2, dual array microphone, combo audio/microphone jack |
| Keyboard         | 6-row, spill-resistant, multimedia Fn keys, LED backlight                                                  |
| Battery          | Integrated Lithium Polymer 4-cell (52Wh)                                                                   |
</details>

## EFI Status
**READ BEFORE PROCEEDING WITH THE INSTALL!**

<details>  
<summary><strong>✅ What works?</strong></summary>
</br>

- [X] Means that the feature works out of the box

- [ ] Means that the feature works *after tweaks* OR *with slight issues*, read the **Miscellaneous Notes** section below for more information

- Means that the feature works depending on your *hardware / addon* configuration, read the **Miscellaneous Notes** section below for more information
<br>

- [ ] Wi-Fi & Bluetooth
- [ ] Audio & Built-in Microphone
- [ ] Brightness Control / Volume Control
- [X] Battery
- [X] Built-in Camera
- [X] Graphics Acceleration

- [ ] Trackpoint / Touchpad

- [X] FaceTime / iMessage (incl. iServices)

- [ ] Sleep / Hibernation

- External Display Output(s) & USB Ports
</details>
<br>

<details>
<summary><strong>⚠️ What doesn't work?</strong></summary>
</br>

- [ ] Continuity Features <br>(AirDrop, AirPlay, Handoff, etc. this is because we don't use AirportItlwm and there isn't any current patch available for Tahoe)
</details>
<br>

<details>
<summary><strong>❌ What WON'T get fixed?</strong></summary>
</br>

- [ ] Fingerprint Reader <br>(No kext to spoof Touch ID, also pretty sure it's tied to TPM which there isn't a kext for anyway as far as I know)
- [ ] DRM, Consider it half broken, it does work on Apple Music (e.g. Loseless Audio), however Dolby Atmos, Safari DRM and TV+ don't seem able to work properly (If you don't even use Apple Music you may remove the `unfairvga=3` from `boot-args` for slightly better iGPU performance)
</details>
<br>

<details>
<summary><strong>📋 Miscellaneous Notes</strong></summary>
</br>

- About Wi-Fi & BT... <br>itlwm w/ HeliPort app, read the **Post-Install** section

- About Audio & the Built-in Microphone... <br>Requires OCLP-mod for AppleHDA patching, read the **Post-Install** section

- About the Display Brightness (Brightness Control)... <br>Don't go to the lowest brightness setting as this will make the display completely black, volume/mic control works as expected, just follow the *Enable Multimedia Keys, Fan Control & LEDs Control* section in the **Post-Install** section

- About iGPU (Graphics Acceleration)... Read the *DRM* section under **What WON'T get fixed?** section if you'd like to slightly improve iGPU performance and *DON'T* use Apple Music

- About the Trackpoint / Touchpad... <br>The trackpoint and pad is buggy when waking up from sleep after a very short period 1~2 minutes, read the **Post-Install** section to fix the gestures

- About external display outputs... <br>HDMI works, however mini DP is currently untested but it should work as the framebuffer accounts for it, con0 (LVDS), con1 (mini DP) and con2 (HDMI), these connectors were all cross-referenced via [*Hackintool*](https://github.com/benbaker76/Hackintool)

- About Sleep / Hibernation... <br>To get proper S3/S4 sleep working, read the **Post-Install** section

- The ThinkPad dock USB ports/display out ports aren't mapped since I don't have one you may follow the official [Dortania Guide Connector types patching guide](https://dortania.github.io/OpenCore-Post-Install/gpu-patching/intel-patching/connector.html) (To fix the display outputs if they don't work) and use [USBMap](https://github.com/corpnewt/USBMap) (To map the missing USB ports on the dock)

- Fingerprint Reader doesn't work when booting into Windows from OpenCore (This also shows the TPM 2.0 module with *Code 10* in the Windows Device Manager, disabling Intel PTT might fix this haven't tested this yet)
</details>

# Installation<br/>

<details>  
<summary><strong>How to install macOS</strong></summary>
</br>

**NOTE: <br>For the *`ROM`* in the *`config.plist`* you should use your laptops MAC address as mentioned in the Dortania guide**
1. [Create an installation media](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer) <br>However, macOS Tahoe doesn't seem to be on Dortania yet (or OpenCore 1.0.5) so use the [*`macrecovery`*](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/blob/main/Resources/macrecovery.zip) (and extract it of course) from this EFI repo which will download the latest Tahoe online .dmg

```bash
macrecovery.py -b Mac-CFF7D910A743CAAF -m 00000000000000000 -os latest download
```

2. Download the [latest EFI folder](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/releases), copy the EFI folder to a USB drive, but before following Dortania's macOS install guide (i.e. before copying com.apple.recovery.boot and installing) you MUST do the following:
- Download [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)
- Start GenSMBIOS
- Select the `"2. Select config.plist"` option
- Drag the config.plist from your USB in
- Select the `"3. Generate SMBIOS"` option
- Type in `"MacBookPro13,1"`
- Then press enter and after that you're ready to install macOS
3. Change your BIOS settings according to the table below

| Menu     |                   |                                 | Setting                                                                                                                                                                                                                 |
| -------- | ----------------- | ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Config   | USB               | UEFI BIOS Support               | `Enabled`                                                                                                                                                                                                               |
|          | Power             | Intel SpeedStep Technology      | `Enabled`                                                                                                                                                                                                               |
|          |                   | CPU Power Management            | `Enabled`                                                                                                                                                                                                               |
|          | CPU               | Hyper-Threading Technology      | `Enabled`                                                                                                                                                                                                               |
| Security | Security Chip     |                                 | `Disabled` - Enabling this will break TPM passthrough to Windows (breaking fingerprint by extension as well) when booting from OpenCore, also if you're gonna use it anyway use Intel PTT as the other TPM breaks sleep |
|          | Memory Protection | Execution Prevention            | `Enabled`                                                                                                                                                                                                               |
|          | Virtualization    | Intel Virtualization Technology | `Enabled` - I keep it *Disabled* it for better performance                                                                                                                                                              |
|          |                   | Intel VT-d Feature              | `Enabled` - Same as *Intel Virtualization Technology*                                                                                                                                                                   |
|          | Anti-Theft        | Computrace                      | `Disabled`                                                                                                                                                                                                              |
|          | Secure Boot       |                                 | `Disabled`                                                                                                                                                                                                              |
|          | Intel SGX         |                                 | `Disabled`                                                                                                                                                                                                              |
|          | Device Guard      |                                 | `Disabled`                                                                                                                                                                                                              |
| Startup  | UEFI/Legacy Boot  |                                 | `UEFI Only`                                                                                                                                                                                                             |
|          | CSM Support       |                                 | `No`                                                                                                                                                                                                                    |
|          | Boot Mode         |                                 | `Quick`                                                                                                                                                                                                                 |

4. Boot from the USB installer (press `F12` to choose boot volume) and [start the installation process](https://dortania.github.io/OpenCore-Install-Guide/installation/installation-process.html#booting-the-opencore-usb)

5. Install macOS as usual, however since Wi-Fi doesn't work in the Recovery USB so you will have to do 1 of 2 things:
- Use an Android device and enable USB tethering (If you have an Android phone) 
- Use a USB A to Ethernet cable adapter (If you have no phone or an iPhone)

6. Now if you have booted into macOS, proceed to the post-install section
</details>

## Post-Installation

### Required (!) Tweaks

<details>
<summary><strong>Fixing Wi-Fi with HeliPort</strong></summary>
</br>

1. Download [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases/download/v1.5.0/HeliPort.dmg)
2. Run the downloaded .dmg and then drag and drop HeliPort.app into the Applications folder
3. Launch HeliPort from Spotlight/Applications folder in Finder
4. Hold the *Option key* (*Windows* key) while clicking the HeliPort Wi-Fi icon
5. Check the `Launch at login` option
6. Hold the *Option key* (*Windows* key) and drag the regular Wi-Fi icon (3 signal bars) out the menu bar
</details>

<details>
<summary><strong>Fixing Audio with OCLP-mod</strong></summary>
</br>

**NOTE: <br>Do NOT use regular OCLP as it WILL fail patching for AppleHDA since Tahoe is unsupported as of now**
1. Download [OCLP-mod](https://github.com/laobamac/OCLP-Mod/releases/download/push-e02886b6697775a95b14dae10e16684c6bdb9c24/OCLP-Mod.pkg) (Please keep in mind this app is in Chinese so you might want to use a live image translator like Google Lens)
2. Run the downloaded .pkg and follow the installation instructions
3. Launch OCLP-mod
4. Click the 2nd button (Top right, with a screwdriver and wrench icon)
5. Click the top button (This will patch AppleHDA back in and auto-download the KDK which is needed)
6. It should prompt you with a pop-up with another blue button after everything is done click it, which should reboot the system and after the reboot audio should now work
</details>

<details>
<summary><strong>Enabling proper Sleep/Hibernation</strong></summary>

**NOTE: <br>Sleep & Hibernation on macOS is a bit weird, I don't know 100% know if the current behaviour we have is intended and I've researched how S3/S4 is handled in macOS and I **THINK** it works fine (Since it doesn't drain or wake randomly).** <br>Before moving on, IF you've ever messed with pmset run the following command before moving to Step 1, if not you can just skip this step:
```bash
sudo pmset restoredefaults
```

1. Run the following commands:

```bash
sudo pmset -a hibernatemode 3
sudo pmset -a autopoweroff 0
sudo pmset -a powernap 0
sudo pmset -a standby 0
sudo pmset -a proximitywake 0
sudo pmset -a tcpkeepalive 0
sudo pmset -a standbydelaylow 3600
sudo pmset -a standbydelayhigh 10800
sudo pmset -a standby 1
sudo pmset -a sleep 1
```

1.5. If you want full hibernation to disk run every command **EXCEPT** the *`hibernatemode`* command and run this one instead (Not recommended due to the strain that will be placed on the hardware every sleep/wake cycle)

```bash
sudo pmset -a hibernatemode 25
```
</details>

<details>
<summary><strong>Enable Multimedia Keys, Fan Control & LEDs Control</strong></summary>
</br>

1. Download and install [YogaSMC-App-Release.dmg](https://github.com/zhen-zen/YogaSMC/releases) <br>(Both the pref-panel and app itself, however the pref-panel won't start after logout/restart if you use the shortcut in the menu bar, just go to the pane from settings if you don't wanna reinstall the pane EVERY login/reboot)
2. Open the app
3. Check the `Launch on login` option
4. Open the pref settings and copy the following settings for all the LEDs/features to work <br>(The LEDs will be stuck in the *sleep* state after waking from Sleep/Hibernate if you don't do this)
![ysmc-p1](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/blob/main/Resources/README/yogasmc-p1.png)
![ysmc-p2](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/blob/main/Resources/README/yogasmc-p2.png)
</details>

<details>
<summary><strong>Fix Trackpad Gestures</strong></summary>
</br>

1. Due to some stupid limitations we have to do this manually so first of all open up the Settings app
2. Go to the *Trackpad* section
3. Copy the settings below
![trackpad-1](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/blob/main/Resources/README/trackpad-1.png)
![trackpad-2](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/blob/main/Resources/README/trackpad-2.png)
![trackpad-3](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/blob/main/Resources/README/trackpad-3.png)
</details>

<details>
<summary><strong>Custom CPU Power Management Profile - Depends on system config (Read before moving on!)</strong></summary>
</br>

**NOTE: <br>So the current config is for a i7-6500U (MacBook Air-style config), if you'd like a higher power config (MacBook Pro, for example) or have a different CPU you must regenerate the SSDT by following the instructions below**
1. Run the following script in Terminal

```bash
git clone https://github.com/corpnewt/CPUFriendFriend; cd CPUFriendFriend; chmod +x ./CPUFriendFriend.command; ./CPUFriendFriend.command
```

1. When asked, select preferred values
2. From the pop-up window, copy `ssdt_data.aml` into `/EFI/OC/ACPI/` folder **(Rename it to `SSDT-CPUPM-X1C4.aml`)**
3. Reboot the system
</details>

### Recommended Tweaks

<details>
<summary><strong>Enable HiDPI (Retina Screen)</strong></summary>
</br>

1. Run the following script in Terminal

```bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/xzhih/one-key-hidpi/master/hidpi.sh)"
```

2. Follow the instructions, then reboot

[BetterDisplay method, haven't tested it yet](https://github.com/waydabber/BetterDisplay)
</details>

<details>
<summary><strong>Monitor Internal Temps & Power Draw</strong></summary>
</br>

1. Download and install [HWMonitor](https://github.com/kzlekk/HWSensors/releases)
2. Check `Launch on login` (Optional)
</details>

### Optional Tweaks

<details>
<summary><strong>Improve Speaker Quality (EQ)</strong></summary>
</br>

1. Acquire [SoundSource](https://rogueamoeba.com/soundsource/) Pro (otherwise you will have noise after 20 mins)
2. Copy the following settings
![ss-1](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/blob/main/Resources/README/soundsource-1.png)
![ss-2](https://github.com/saku-bruh/ThinkPad-X1-Carbon-G4-OpenCore/blob/main/Resources/README/soundsource-2.png)
3. Remove the regular volume dialog by holding the *Option* key (*Windows* key) and dragging it out of the menu bar
</details>

<details>
<summary><strong>Boot Process Tweaks</strong></summary>
</br>

| Menu |       |            | Setting      | What does it do?     |
| :--- | :---- | :--------- | :----------- | :------------------- |
| Misc | Boot  | ShowPicker | `False`      | Skip bootloader page |
| UEFI | Audio | PlayChime  | `Disabled`   | Always silent boot   |
</details>

# Updating macOS

<details>  
<summary><strong>How to update macOS</strong></summary>
</br>

**WARNING, NOT FULLY TESTED, RISK OF DATA LOSS AND/OR TOTAL INSTALL DESTRUCTION:** <br>I only tested this once before <br>(26.0 > 26.0.1 update and it resulted in a brick after re-applying the root patch for AppleHDA) and I didn't click the blue button (from OCLP-mod) during the OTA and didn't reboot once after updating, which probably broke the APFS seal, clicking the button and rebooting once should probably fix the issue I had and it might even auto-install the audio patch, however I reccommend waiting until your macOS version is supported by OCLP-mod so a mismatch doesn't happen, be **extremely** cautious and update at your own risk

1. Open OPLC-mod
2. Click the 2nd button (top right corner)
3. Click the 2nd button (You can use Google Lens to translate, it should say something like "Revert Root Patch")
4. Wait for the process to finish
5. Reboot when it asks you to
6. Go to Settings, start the update and wait, at some point it will give you a prompt with a blue button and you should click that
7. Wait for the process to finish the update fully, then login and then just reboot
8. Re-apply the root patch (If it hasn't done so already)
</details>


### Credits

[Dortania Guide](https://dortania.github.io/OpenCore-Install-Guide)

[OpenCore-Simplify](https://github.com/lzhoang2801/OpCore-Simplify)

[duszmox's ThinkPad X1 Carbon Gen 4 EFI](https://github.com/duszmox/ThinkPad-X1C4-macOS-OpenCore) for SOME SSDTs/Patches and general kext layout

[anathonous' ThinkPad X1 Carbon Gen 5 EFI](https://github.com/anathonous/X1C5-Hackintosh-OpenCore-MacOSX) for working SSDTs/Patches and one part of the framebuffer

[SkyrilHD's Fujitsu EFI](https://github.com/SkyrilHD/FUJITSU-ESPRIMO-Q956-Hackintosh) for the other part of the framebuffer

[Hackintool](https://github.com/benbaker76/Hackintool) for helping with the connector patching