# Pico 4 Debloat
A guide on how to debloat the Pico 4 VR headset to improve performance and privacy.

The ones on the recommended list have been tested by me and made the headset snappier, while also deleting some useless features (like the guide you only use during the first time setup of the headset)

### First, turn on the Developer Mode on your Pico 4 headset
1. Put on your headset and go to **Settings**.
2. Select **General** and then **About**.
3. Scroll down to **Software Version** (or Build Number) and click it repeatedly (7 to 10 times) until a message says "You are now a developer".
4. Go back to the **General** menu, find the new **Developer** options, and toggle **USB Debugging** to ON.

## How to debloat your Pico 4
1. Download the [Android SDK Platform Tools ZIP](https://developer.android.com/tools/releases/platform-tools) from Google.
2. Extract the folder to a known location on your PC.
3. Connect the Pico 4 to your PC.
4. On your PC, open the extracted `platform-tools` folder.
5. Right-click an empty space inside the folder and select **Open terminal here** (or Open PowerShell).
6. Type `./adb devices` and press Enter to ensure your Pico 4 is listed.
7. Type `./adb shell` and press Enter to access the headset's internal command line.
8. Copy the commands you want from the lists below, paste them into the terminal and press Enter.

*Note: If you get an "Unknown package" error, it just means that specific app is already missing from your software version; you can safely ignore it.*

## General recommended commands
These are the commands I recommend using to make your Pico 4 faster and cleaner without breaking anything.
```
pm disable-user com.android.bips
pm disable-user com.android.bookmarkprovider
pm disable-user com.android.carrierconfig.overlay.common
pm disable-user com.android.egg
pm disable-user com.android.ondevicepersonalization.services
pm disable-user com.android.printservice.recommendation
pm disable-user com.android.providers.blockednumber
pm disable-user com.android.providers.userdictionary
pm disable-user com.android.role.notes.enabled
pm disable-user com.android.safetycenter.resources
pm disable-user com.android.server.telecom.overlay.common
pm disable-user com.android.simappdialog
pm disable-user com.android.theme.font.notoserifsource
pm disable-user com.android.traceur
pm disable-user com.android.uwb.resources
pm disable-user com.android.wallpaperbackup
pm disable-user com.android.wallpapercropper
pm disable-user com.pvr.tobactivate
pm disable-user com.qualcomm.qti.dynamicddsservice
pm disable-user os.teatracker
pm disable-user com.sohu.inputmethod.sogou.car
pm disable-user com.picovr.guide
```
#### Network & UI Tweaks  
Improves button responsiveness and prevents Wi-Fi from acting autonomously.
```
settings put global auto_wifi 0
settings put global netstats_enabled 0
settings put global wifi_wakeup_enabled 0
settings put secure long_press_timeout 250
settings put secure multi_press_timeout 250
settings put global settings_enable_monitor_phantom_procs false
```
#### Performance & Process Management
Optimizes how the system handles background apps and CPU priority.
```
device_config set_sync_disabled_for_tests persistent
device_config put activity_manager bg_auto_restrict_abusive_apps 1
device_config put activity_manager bg_current_drain_auto_restrict_abusive_apps_enabled 1
device_config put activity_manager max_cached_processes 256
device_config put activity_manager max_phantom_processes 2147483647
pm bg-dexopt-job
```
#### If your firmware is older than 5.13.7.S
These are TOB (To Business) enterprise management services, redundant for home users and only used for corporate fleet control or kiosk modes.
Unfortunately Bytedance locked them from 5.13.7.S onwards, but it may be useful for those with older firmwares.
```
pm disable-user com.bytedance.pico.tob.userservice
pm disable-user com.picovr.enterpriseassistant
pm disable-user com.picovr.tobvrusercenter
pm disable-user com.picoxr.tobmdm
pm disable-user com.picoxr.tobstore
pm disable-user com.pvr.tobhome
pm disable-user com.pvr.tobservice
```

#### Optional commands (disable core Pico apps)
```
Files: adb shell pm disable-user com.pvr.filemanager
Avatar: pm disable-user com.pvr.avatareditor
Fitness: adb shell pm disable-user com.pvr.pvrfit
Browser: pm disable-user com.pico.browser.overseas
Friends: pm disable-user com.picopui.im
Explore: adb shell pm disable-user com.pvr.home
Screencast: adb shell pm disable-user com.pvr.picocast

System update: 
pm disable-user com.picovr.firmwareupdate
pm disable-user com.picovr.updatesystem

PICO Connect:
pm disable-user com.picovr.picostreamassistant
pm disable-user com.picoxr.bstreamassistant
```

---

## Commands I recommend skipping
These commands were excluded from the main list due to potential side effects.
```
pm disable-user com.android.inputmethod.latin - **DO NOT** run unless you have another keyboard installed.
pm disable-user com.android.hotspot2.osulogin - Disables captive portals. If you don't use public Wi-Fi (airports/hotels), you can disable it.
pm disable-user com.android.musicfx - Disables system EQ; may degrade sound quality.
pm disable-user com.picoxr.mirrorcast - Breaks native screen casting to TVs.
pm disable-user com.pvr.lanserver - Breaks discovery via the Pico smartphone app.
pm disable-user com.qualcomm.wfd.service - Likely to break wireless projection (Miracast/Cast).
pm disable-user com.qualcomm.qti.qms.service.trustzoneaccess - May break Store purchases and DRM (Netflix/YouTube).
pm disable-user vendor.qti.qesdk.sysservice - Disables low-level Qualcomm engineering diagnostics.
pm disable-user com.quicinc.voice.activation - May break voice-to-text features.
pm compile -a -f -m speed - Improves speed but significantly increases storage usage.
```
The following 4 deal with the screen's color calibration, brightness and contrast
Disabling them might change image quality.
```
pm disable-user com.qti.service.colorservice
pm disable-user com.qti.snapdragon.qdcm_ff
pm disable-user com.qualcomm.qti.qcolor
pm disable-user com.qti.dcf
```

## Need to revert all actions?
You can replace `pm disable-user` with `pm enable` for each package and run that command. Example: `pm disable-user com.android.bips` becomes `pm enable com.android.bips`

Then, run the commands below.

```
settings delete global auto_wifi
settings delete global netstats_enabled
settings delete global wifi_wakeup_enabled
settings delete secure long_press_timeout
settings delete secure multi_press_timeout
settings delete global settings_enable_monitor_phantom_procs
device_config set_sync_disabled_for_tests none
pm bg-dexopt-job
```
---

## An explanation about `pm compile -a -f -m speed`
Think of each APK as a compressed book written in a foreign language. Normally, every time you open an app, the headset has to translate it into the processor's native language while you're using it (that system is called JIT, Just-In-Time). This constant translation during gameplay is what causes longer loading times and occasional stutters.​

When you run `pm compile -a -f -m speed`, you are telling the headset to translate the entire book and save it in the processor's language permanently (AOT, Ahead-Of-Time).

Since the processor no longer needs to translate on the fly, apps load faster and run much more fluidly, and it may help saving the battery too. The trade-off is that this translated version takes up significantly more storage space than the original compressed APK.

That command will compile all the apps currently installed, but will not affect future installs. In case you want to do it just to one app, you can run this instead:
`adb shell pm compile -m speed -f com.pico.browser`

Just replace the `com.pico.browser` with the name of the package you want and you're set


