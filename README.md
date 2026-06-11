# lazy-phone-user
Tasker project with llamalab automate flows for the average lazy phone user who wants their phone self-handled or self-managed. works on rooted & non-rooted (which works via UI simulation or via working/reliable helper apps to do things that require root) phones. configure once & forget until maybe few more apps to be plugged in to relevant profiles

### TLDR of what this workflow does:
toggle/auto-set wan connectivity/maps relevant settings/low power & airplane modes/cpu settings/bluetooth/volumes/tethering settings/obd2 based on set apps/notifications/global vars.s/conditions/settings. or better way to put it is that all those settings toggled and or actions performed by the user before performing the needed actions/tasks on an app the workflow does it for you


### Required apps
 1. Tasker
 2. [TaskerSettings](https://github.com/joaomgcd/TaskerSettings) (api level check based actions & tasks are created based on what's written in this page)
 3. llamalab automate
 4. autoinput
 5. auto notification. 

# Disclaimers:
1. There maybe slight delays in running tasks triggered by the low power mode profiles which send intents to llamalab automate for weaker phones.
2. if at times actions/tasks don't run, try performing said action/task from the `run an action` option from tasker just to see if the action's being run which if it doesn't, try one few or all of the below
    - reboot
    - disable tasker, reboot & `run an action` as a test which if it does work then enable tasker
3. if certain profile's actions/tasks aren't triggered, try performing actions/op.s that gets the profile inactive & back active just to get the task/actions re-triggered
4. There are tasks that are specific to doing one thing which could've been just one action but no thanks to the android drama going on, I've had to set some checks for SDK level < 29, root & or shizuku being there which if the conditions are satisfied would be one action for things that require root/shizuku else it would either be an entire user input simulation through autoinput or no action at all with just a tts/toast msg saying "x is left enabled/disabled" depending on the display's state (lock set/unset)
5. When importing Automate flows, the flow uri's may differ so you will have to change the uri values in the tasker actions that send intents to the respective Automate flows. What tasker `send intent` action sends intents to what flow in automate is mentioned as a label on that tasker action's label param
6. profiles to be mutually exclusively enabled/deleted when testing or using are to be done so to avoid clashes, confusion in what to use or any other unexpected behaviours.
7. You may need to recreate profile contexts in case it still gives you warnings of apps not existing even after unselecting everything.
8. Profiles that use the app context may not be completely reliable for apps open in floating/small windows..android limitation...logcat entry on didn't work on my non-rooted phone & the WIN var also isn't too reliable...so gotta renter the app or "re-exit" the app that was previously open to get such profiles active
9. Install & configure a TTS engine LIKE google's `speech recognition & synthesis` if none installed & configured already. test the `say` action if you need any voice. The say actions have the "continue task after error" option enabled so the tasks don't fail due to a voice msg action failure, only disadvantage is that the user would not know if the tasks with the say actions actually ran or rightfully ran. A notification will be posted saying what to do should the `say` action fail.

# setup
#### a. import the automate flows (the `.flo` files from the `Automate flows` directory) into automate & the tasker project `Lazy_User.prj.xml` into Tasker
#### b. <b>list of profiles to be mutually exclusively enabled/deleted</b>
1. `Mobile Data Var Set via Automate` & `Mobile Data Var Set`
2. `(Un)Set Sim Presence Airplane Mode via Automate` & `(Un)Set Sim Presence Airplane Mode`
3. `Wifi Hotspot State Via Automate Via Automate` & `Wifi Hotspot State`
#### c. <b>Tasker Configuration</b>
1. copy `app_ctx_invisible_apps_for_fg_unset_profile.txt` into `/sdcard/Tasker/projects/` or wherever you like but the path where you copy this file must be the set in the `Read File` action's (action 1) `File` arg of the anonymous task belonging to the `App Context Invisible Fg Apps` profile.
2. Toggle data & bluetooth from tasker's `run an action` option (3 dots on top right location of the main activity -> More -> Run an action) & select the "never ask again button" from the popup you get for TaskerSettings helper app to work without autoninput's "simulation" at work (possibly....didn't work reliably on my non-rooted android, only changed the UI state in the quick settings tile & mobile data settings. no actual system/settings level changes), which if it doesn't, the autoinput simulation will be put to work (on non-root android), else the bluetooth & wifi "toggle" action alone will do the work if root/shizuku is available/running.
3. set your apps you always need WAN connectivity for when in foreground/opened in the `Fg Net Flags Set` profile's app context or recreate it (ANY & EVERY app that you ABSOLUTELY DEEM needing net including email clients, net scanners, maps, games, browsers, ftp clients etc. all goes here) & the same is applicable for `Fg Net Flags Set (Optional)` profile except it's for apps that you deem as MAY NEED WAN connectivity which is decided through a prompt
4. set your email client in the `Email Auto Sync (Un)Set` profile's App context (Also needs to be set in the `Fg Net Flags Set` or `Fg Net Flags Set (Optional)` and or in the "Apps" section of autonotification event context of the `Bg Net (Un)Set` profile)
4. `Fg Net Flags UnSet` - recreate the app context with the invert option selected & set `mobile data settings` app or the `settings` app itself & any other apps you DON'T want this profile's app context to be picked up on while being in that app/activity. Idea is something along the lines of "need wan connectivity for f-droid to download stuff (which is already downloading) & all of a sudden get a call & the call activity takes the foreground over f-droid's activity & you don't want that wan connectivity to be disturbed while the call activity is in the foreground & until the download is complete. I have set only play store & whatsapp on both activities & added some settings activities that the app profile trigger showed in the `Fg Net Flags UnSet` profile to make it more easier to set up
5. set your apps (for notification) in the `Bg Net (Un)Set` profile's auto notification event...idea is to set WAN connectivity enabled for those apps you would want the network to be left enabled until done, like downloads
6. set your games in the `Gaming` profile's app context
7. set the map apps you use in the `Map Bg Flags (Un)Set` profile's app context
8. Open up `Toggle Data` task & replace the actions in the Autoinput Action (the configuration argument) at action number 15 with whatever actions you need to perform to get cellular data enabled from the `Mobile Data settings` activity. (the activity, button placement, UI differs from android flavour to flavour)
9. <b>(optional if this is not needed)</b> remove/disable the `Work Profile Set` profile & if needed/wanted remove the WORK_PROFILE global var from all other actions, tasks, conditions & profiles (this is necessary IF the `Work Profile Set` profile's removed). The action for it is created the way it is for when corporate apps are installed onto a work profile & one needs to show their activity as online at all times or needs to be responsive & needs connectivity at all times as long as there's some source of connectivity & for as long as the work profile's active.
10. `Gaming Clock`, `Regular Cpu Clock` & `Low Clock Cpu`. Reconfigure the cpu list with their respective low & high clocks & governors. (I have set my `Gaming Clock` task to the highest clock that was available with all core governors to performance & the numbers you're seeing are ones on my unrooted phone) & the `Regular CPU Usage` all to schedutil & `Low Clock Cpu` all to powersave & all cores to whatever I could get the lowest number at which is in Khz & something I pulled from my unrooted if you're wondering why that many zeros in the clock numbers (whatever governor values you're seeing are the only governors available for both my phone's kernels). you're gonna need to delete/add some cpu actions & change their params depending on your phone's hardware
11. `(Un)Set Sim Presence Airplane Mode` or `(Un)Set Sim Presence Airplane Mode via Automate` (profiles to be mutually exclusively enabled). The only difference between the 2 profiles is that the Action in the `intent received` event from both profiles are
    - android's built-in `SIM_STATE_CHANGED` (used by `(Un)Set Sim Presence Airplane Mode`) broadcast is said to be unreliable since android 13 (https://issuetracker.google.com/issues/302614301). I haven't faced that issue on android 13. Only "weird" thing is that I've been seeing that profile being triggered with that broadcast many times even when I haven't ejected/inserted the sim card
    - `SIM_PRESENT` (used by `(Un)Set Sim Presence Airplane Mode via Automate`) is a custom broadcast sent from automate to tasker & the `Tasker - Sim Check.flo` needs to be left running for this profile & it's task to work as expected
12. `Mobile Data Var Set via Automate` or `Mobile Data Var Set` (to be mutually exclusively enabled or deleted by the user)
    - `Mobile Data Var Set` - do a `find setting` (the magnifying glass next to the `Name` field) in the profile's `custom setting` context, toggle cellular data & you got the custom setting name that gets you the cellular data state filled in the Name field
    - `Mobile Data Var Set via Automate` will need the `Cellular Data state` flow beginning from `Tasker - settings & states checks` to be left running
13. (disable/delete if not needed which you would need to do to the `OBD2 (Un)Set` profile as well) set your obd apps as the entry app in `OBD2 Fg Flags (Un)Set` profile & the very same obd apps as auto notification entry in `OBD2 Bg Flags (Un)Set` profile
14. At `Ping Test` task you might want to make changes in the numbers at action 14 & 18 (conditional actually) if you think the ping numbers don't apply to your use case
15. you may need to change the `Data` field in the `Send Intent` action in all tasker tasks which are intended to invoke Automate flows (the flow to be invoked will be indicated in the `send intent` action's label field) to whatever the content has been changed to in the flow's start block. do this IF the "Flow URI" field in the imported automate flow's start block differs from what's there in tasker's `send intent` action's Data field.
16. reboot the phone or run the `Check Set Stuff At Boot` TASK to initialize global vars & run the needed automate flo's based on the selected profiles
17. <b>(optional/if needed)</b> Enable the actions in the 2nd task of the `screen Off Low Power` & `screen Unlocked No Low Power` profiles
18. if you do not want the toasts from the `Ping test` & `WAN check` the actions to disable/delete are the following
     - `Ping Test` Task
        - action 3
        - action 6
        - action 11
        - action 17
        - action 20
        - action 22

     - `WAN Check Switch` Task
        - action 3
        - action 19
        - action 24
        - action 28
        - action 37
        - action 40
        - action 51
        - action 59

## What each Tasker profiles do
##### 1. <b>workflow base & entry point. stuff profiles here can be used to initiate/start the workflow & be used as a common entrypoint for implicit intents to trigger the profiles/tasks/actions</b>
* `Check Set Stuff At Boot` - after boot (basically unlock after boot), initializes stuff for the workflow, like setting global vars & enables/disables profiles as needed for the workflow to work right. Also uses the `Tasker - Check Set boot stuff.flo`
* `Custom Intent Recvr` - a "sinkhole"/"dump"/"intent slot"/"globally open slot for intents" of sorts for implicit intents (I use this to set global var.s & toggle profiles......for now atleast). There's a java code action in there to pull out keys/values from an intent payload that has multiple key/value pairs.

##### 2. <b>global var setter profiles for phone settings</b>
* `wifi var set` - set wifi stats as global variable(s) whenever wifi is toggled (used by the net management profiles)
* `Shizuku Var Set` - set Shizuku Var Set stats as global variable (used by profiles/tasks to check if it's running & then perform actions that require root/shizuku)
* `VPN state` - set vpn stat as global variable. sure I could have used `VPN_CONNECTED` but that var wasn't available until I used the `VPN Connected` context for this profile
* `Adb Wifi State Var Set` - set Adb Wifi State stats as global variable (used by the net management profiles)
* `Bluetooth Connection Check` - trigger to get connection/disconnection event. wait for 15 seconds before disconnecting. the wait time is to wait for any other connections or for a reconnection
* `Low Power State` - set global var of low power state.
* `Phone Unlock State` - set the global var of phone's screen state when phone is unlocked.
* `Phone Lock State` - set lock state of the phone into relevant global var.s. Evaluated when screen is off.
* `Work Profile Set` - toggle settings, var.s & values for your work profile
* `Mobile Data Var Set` - set mobile data stats as global variable(s) whenever data is toggled (used by the net management profiles)
* `Mobile Data Var Set via Automate` - set mobile data stats as global variable(s). (used by the net management profiles but triggered by intents sent from automate. Triggering flow beginning `Cellular Data state` in flow `Tasker - Settings & States Checks`)
* `Wifi Hotspot State` - set wireless hotspot state via `WIFI_AP_STATE_CHANGED` broadcast
* `Wifi Hotspot State Via Automate` - same `Wifi Hotspot State` but with automate (Triggering flow beginning `Hotspot state` in flow `Tasker - Settings & States Checks`)
    

##### 3. <b>profile for airplane mode based on sim presence</b>
* `(Un)Set Sim Presence Airplane Mode` - toggle airplane mode when sim inserted/ejected. Uses android's `SIM_STATE_CHANGED` broadcast
* `(Un)Set Sim Presence Airplane Mode via Automate` - toggle airplane mode when sim inserted/ejected. custom broadcast `SIM_PRESENT` is sent by automate by the flow beginning `Sim presence` in flow `Tasker - Settings & States Checks`. The flow must be kept running for this profile to work

##### 4. <b>WAN iface toggle</b>
* `Fg Net Flags Set` -  set global variable(s) to enable wan connectivity for net requiring apps running in the foreground <b>(just flag setting for `Net Set` & `Net Unset` profiles)</b>
* `Fg Net Flags Set (Optional)` - Same as Fg Net Flags Set except the user will be prompted for if WAN has to be enabled/disabled which (un)sets the same flags by the `Fg Net Flags Set` profile AND an `FG_NET_APP_OPT` to nonet, var is cleared in 15 seconds of being out of the app, when `Fg Net Flags Set` profile is active or when wan is already available, which the user will be prompted again if all of the aforementioned conditions are unsatisfied. <b>(just flag setting for `Net Set` & `Net Unset` profiles)</b>
* `Fg Net Flags Unset` - set stats as global variable(s) to disable wan connectivity for when net requiring apps are not running in the foreground. There's a delay of 15 seconds for the `Fg Net Flags Set` profile to be active which if it isn't, the global var `%APP_NET` is to `no` which satisfies one of the `set variable` context's conditions from the `Net Unset` profile. <b>(just flag setting for `Net Set` & `Net Unset` profiles)</b>
* `Bg Net (Un)Set` - set/unset stats as global variable(s) to toggle WAN connectivity. Now limited to downloading & navigation. Actions 2 to 7 are disabled in case there are cases that require rendering the profile to be inactive but setting it in the autonotification context would render either a hassle or impossible. same reason as to why a there's a disabled action that enables said profile in the exit task. enable said actions if such a case occurs. <b>(just flag setting for `Net Set` & `Net Unset` profiles)</b>
* `Wifi Hotspot Autoset Stuff` - when hotspot is set, disables all wan related profiles (in case one wants to change stuff at will)
* `Usb Tether Autoset Stuff` - same as `Wifi Hotspot Autoset Stuff` but applicable for usb tethering
* `Net Set` - toggle wan connectivity based on global var.s values set from `Fg Net Flags Set`, `Fg Net Flags Unset`, `Bg Net (Un)Set`, `Work Profile Set` or `VPN state` profiles. if value is enabled, WAN connectivity is enabled, if disabled then WAN connectivity is disabled. (this is where the WAN connectivity toggling happens)
* `Net Set (User)` - disables all wan TOGGLING/IFACE SWITCHING PROFILES when user enables any wan connectivity while no wan managing profiles are active or have performed actions that DON'T trigger said wan managing profiles to be active (`Net Set`, `Wan Check Switch`, `Ping Test` ,`Net Unset`, `Net Src Disconnected Switch`). enables said disabled profiles when this profile is inactive. this for when user wants to manage WAN connectivity
* `Net Src Disconnected Switch` - switch to data when wifi gets disconnected when a net needing app is running (foreground/background)
* `Redundant Net Src` - when data & wifi are enabled & wifi is connected, disable data. vice versa is also applicable. when no sim, wait for 20 seconds until wifi connection is made else disable wifi
* `Wan Check Switch` - checks if wan is reachable when connected via enabled/active/connected wan iface, which then waits for `Ping Test` to set `HIGH_PING` global var & based on the var's value shows an input dialog to change the ssid to which if user selects "no" prompts to change to cellular data to which if user selects "no" then leaves the iface as is by stopping the profile assigned task. switch WAN src based on ping numbers
* `Ping Test` - checks ping timings (5 pkts) in an infinite loop (goto step 1 is applied)
* `Net Unset` - disable wan connectivity. Active when ALL wan connectivity profiles (`Fg Net Flags Set`, `Fg Net Flags Unset`, `Bg Net (Un)Set`, `VPN state` & `Work Profile Set`) have set their global var.s to satisfy the conditions to set this profile active to disable all wan connectivity
* `No Root Screen Off Data Disconnected` - Task that tells the user to disable data every 10 seconds when screen is locked/off & is not in use until data is disabled (active when root is unavailable or shizuku is not running)

##### 5. <b>Map settings</b>
* `Map Fg Flags (Un)Set` - set global variable(s) to be used to toggle settings for maps in foreground <b>(just flag setting for `Map (Un)Set` profile)</b>
* `Map Bg Flags (Un)Set` - same purpose as `Map Fg Flags (Un)Set` but for apps in background <b>(just flag setting for `Map (Un)Set` profile)</b>
* `Map (Un)Set` - toggle map app settings

##### 6. <b>Bluetooth toggle</b>
* `OBD2 Bg Flags (Un)Set` - set stats for global variable(s) to be used to toggle settings for set obd2 apps running in background <b>(just flag setting for `Bluetooth Set` profile)</b>
* `OBD2 Fg Flags (Un)Set` - same purpose as `OBD2 Bg Flags (Un)Set` but for when the apps running in the foreground <b>(just flag setting for `Bluetooth Set` profile)</b>
* `Bluetooth Set` - to enable bluetooth (dependent on the global var.s set by obd2 & bluetooth check profiles)
* `Bluetooth Unset` - to disable bluetooth (dependent on the global var.s set by obd2 & bluetooth check profiles)
* `No Root Screen Off BT Disconnected` - same as `No Root Screen Off Data Disconnected` but for bluetooth

##### 7. <b>low power based on lock & screen state</b>
* `screen Off Low Power` - set low power mode when screen is off. `Test Display` action to set `PHONE_LOCKED` & `PHONE_LOCK_SET` values & switch bluetooth off if connections are 0
* `screen On Locked` - when phone is locked but screen is up
* `screen On No Lock No Low Power` - disable all low power settings when screen is on & no lock is set
* `screen Unlocked No Low Power` - disable all low power settings when unlocked
* `Charging No Low Power` - when power source is plugged in, disable power save mode

##### 8. <b> Stuff for tasker & workflow </b>
* `Tasker Log Clear` - literally what it says.
* `New App Installed` - to prompt the user if an installed app is to be set in the app context of the profiles in this project to which if pressed yes, will open tasker. will only be a popup if already in tasker
* `Disable Profiles` - disable those profiles used for separation & the profiles that require root.
* `App Context Invisible Fg Apps` - to get app/pkg names that are not listed in tasker's app context & compare the pkg name against pkgs set in `/sdcard/Tasker/projects/app_ctx_invisible_apps_for_fg_unset_profile.txt` (too long a name I know) file which if it's there sets the `APP_CTX_INVISIBLE_FG_NET_UNSET_PKG` global var (yes, it's a mouthful & too wordy...best I could come up with) with that pkg name via grep (done like this "grep <pkg name> | grep -iv '#'"). The global var is then used in the `Fg Net Flags Unset` profile. can be used to set/unset global vars & files besides what's being currently used


##### 9. <b>other stuff</b>
* `Clipboard Link Sanitizer` - literally what it says. for now only "sanitization" it does is on youtube share links in the clipboard which is just taking away the strings matching `?sid.*`
* `Automate Flow Retrigger` - send a START_FLOW intent to automate flows based on enabled profiles that need automate flows to be left running (for cases where automate & or it's flows are killed by the OS)
* `Email Auto Sync (Un)Set` - sets autosync when email client is in foreground, unsets when not in foreground for more than one minute
* `Alarm Vol Auto Set` - set all alarm vol to max when an alarm's about to ring
* `Autoread Whatsapp` - literally what it says (except set your own interval if you need to)
* `Alarmy Call` - to call one at a scheduled time if you forget to (I use this when one asks me to wake them up from a phone call). This profile is toggled by (enabling & disabling the profile & setting the phone number as a global var for the profile to use)  run `Tasker - Alarmy call.flo` from automate to set the number/contact
* `Alarmy Call Disable` - disables `Alarmy Call` profile (and that the `Alarmy Call` also needing to be enabled is also a cndition) when the incoming caller is the same one set in `WAKE_UP_NUM` by the `Tasker - Alarmy Call.flo` flow causing the task to go inactive, which is why I set the action to disable itself
* `Gaming` - cpu state toggles when enter/opening games
* `Airplane Boarding` - settings to toggle when boarding/deboarding an airplane. keep the `Tasker - Airplane boarding.flo` running when enabling this profile
* `Toggle Dev Mode Per App` - disable dev mode when entering apps that complain about it, enable when exiting with "show touches" & adb enabled


##### 10. <b>not as a automation usage but just for tasker interactivity convenience</b>
* `Bottom "Buffer" Profile. Not To Be Used` - literally what it says. except I originally intended to use it as some sort "buffer" so that I could move the profiles around cuz profiles that were last in the task list I had a tough time getting it to move around. now that the AI button's been added which overlays itself on the profile toggle button (the ones that are last in the list), that "buffer" profile (still used as buffer) is also now a profile which will be last in the list who's toggle will remain obstructed instead of the one's that are of use to the user. (TLDR: just a UI inconvenience mitigator). or you could disable the "AI Generation Button Enabled" option by going to the 3 dots on top of the main activity -> UI tab -> "AI Generation Button Enabled".
* the rest of the profiles (mostly being ones containing `=` & words in it like `Net Mgr`) are just profile separators. when making changes to the entry/exit actions, they shouldn't make any changes to how project's workflow.

### what each Taskert tasks do & what profiles trigger them:

`Autoread Whatsapp` - just clicks that "read all" button. As long as the UI stays the same, this won't break, you're free to delete/disable this profile & it's task, does not depend on anything
`Gaming Clock` - sets cpu settings to whatever settings the user considers & has set to performance mode
`Regular Cpu Clock` - sets cpu settings to whatever settings the user considers & has set to balanced mode
`Low Clock Cpu` - sets cpu settings to whatever settings the user considers & has set to low clock/performance mode
`Custom Intent` - task to be run by the `Custom Intent` profile which receives a `net.dinglisch.tasker.INTENT_RCVR_FOR_BASE-STUFF` broadcast from other locations & sets global vars & toggles profiles based on the payload. 
    - payload struct for toggling profile - { "val_type": "profile", "state": [on|off], "profile_name": "<name>" }
    - payload struct for setting global vars - { <key>: <val> }

`Fg Map Actions` - sets location to high accuracey & auto-brightness when a map app is open
`(Un)Set Sim Presence Airplane Mode` - toggles airplane mode based on sim presence 
`All Vol Set` - task to set a certain vol lvl to all audio stream
`Net Set` - runs the `Redundant Net Src` task, when a singular stable wan connection src is made after which the `WAN check switch` task is run
`Lazy Workflow Initializer` - sets global var.s, toggles necessarily needed to be toggled profiles
`Ping Test` - constant ping test only if the `Net Set` profile is active
`Link Sanitizer` - works with clipboard contents containing links (for now, strips sid from youtube links)
`Enable Low Power Mode` - sets airplane mode if no sim, or sim cannot be used for any comms, battery to power save, disable, hotspot, auto sync, data, wifi, auto-rotate, bluetooth, runs the `Low Clock Cpu` task & enables the "disable sensor" via shell if root/shizuku is available
`Disable Low Power Mode` - only disables battery low power mode & sets cpu to `Regular Cpu Clock` 
`Unset All Alarms` - turns off all alarms
`Toggle Data` - if no root/running shizuku or if taskersettings don't work, uses autoinput to toggle data, action 13. (user needs to configure the actions to toggle data in the mobile data settings activity)
`Toggle Bluetooth` - same as `Toggle Data` but for bluetooth
`Net Src Switch` - if wifi is on disable data & vice versa
`Hotspot Autoset Network Profiles` - disable any WAN iface switching/toggling/checking profiles when hotspot is active, enable the profiles when hostspot is inactive
`TTS Test` - task to test if tts works, with a possible fix that will be mentioned in a notification which will be posted if it doens't work
`Screen State Based Msg` - task to post msg via toast/tts based on screen state (screen on - toast, screen off - tts)
`Bluetooth Unset` - if no connections within 15 seconds, disable bluetooth (if sdk > 31 AND root/shizuku is not running toggle via autoinput else use action's toggle)
`Redundant Net Src Switch` - enable data & wifi, if wifi connected disable data, else disable wifi
`Alarmy Call` - task to call a contact (triggered by `Alarmy Call` profile `Tasker - Alarmy call.flo` automate flow)
`Wan Check Switch` - when `Redundant Net Src` profile is not active, based on the `HIHG_PING` global var value set by the `Ping Test` profile & task
`Toggle Profiles` - literally what it says. used in the `Lazy Workflow Initializer` task to xor toggle profiles (Automate depend/independent).
`Toggle Dev Mode sensors` - toggle "disable sensors" (the dev setting) via `service call sensor_privacy 9 i32 [1|0]`. in a non-privileged shell `TASKER_HELPER_FOR_SENSOR_TOGGLE` global var is checked for the "works" value & THEN runs the cmd in shell to toggle the "disable sensors" option. The check (`Lazy workflow initializer`) is done in an unconventional & hacky way which is the cmd's output's passed to `wc -l` which if 1, it works, else it doesn't since the output I got was a hexdump of dot separated java stack traces complain about argument numbers or types & the shell return var ($?) returned 0 even with the failure & therefore this unconventional hacky way of testing.



### custom global vars set & used by profiles & tasks from the above group of profiles
1. `APP_CTX_INVISIBLE_FG_NET_UNSET_PKG`, `PHONE_LOCK_SET`, `NET_SRC_TOGGLE_COUNT`, `APP_NET`, `BG_NET`, `WORK_PROFILE`, `MDATA`, `ADB_WIFI`, `WIFI_CONNECTED`, `WAN_ACCESSIBLE`, `VPN_CONN`, `FG_NET_APP_OPT`, `FG_NET_APP_NAME`, `ROOT_STAT`, `SHIZUKU_RUNNING`, `HIGH_PING` - [network toggle & check profiles](#global-var-setter-profiles-for-phone-settings), [low power & screen lock profiles](#low-power-based-on-lock-&-screen-state) & [global var setter profiles for phone settings](#global-var-setter-profiles-for-phone-settings)
2. `BG_OBD`,`FG_OBD`,`BT_CONNECTED`,  `ROOT_STAT`, `SHIZUKU_RUNNING` - [bluetooth toggle profiles](#bluetooth-toggle) & [global var setter profiles for phone settings](#global-var-setter-profiles-for-phone-settings)
3. `TASKER_HELPER_FOR_SENSOR_TOGGLE`, `LOW_POWER_MODE`, `PHONE_LOCKED`, `ROOT_STAT` - [low power & screen lock profiles](#low-power-based-on-lock-&-screen-state) & [global var setter profiles for phone settings](#global-var-setter-profiles-for-phone-settings)
4. `BG_MAP`, `FG_MAP` - [Map settings toggling profiles](#map-settings)
5. `WAKE_UP_NUM` - [other stuff](#other-stuff)
6. `TASKER_HELPER_FOR_SENSOR_TOGGLE`, `TASKER_HELPER_FOR_DATA_TOGGLE`, `TASKER_HELPER_FOR_SENSOR_TOGGLE` - [other stuff](#other-stuff), [network toggle & check profiles](#global-var-setter-profiles-for-phone-settings), & [base stuff](#workflow-base-&-entry-point.-stuff-profiles-here-can-be-used-to-initiate/start-the-workflow-&-be-used-as-a-common-entrypoint-for-implicit-intents-to-trigger-the-profiles/tasks/actions)
### global vars (more like flags) & what they mean/are meant for...with values that have been used to get the workflow to run
* `APP_NET` (yes|no) - value is toggled when a wan connection needing app is or is not in the foreground
* `BG_NET` (yes|no) - same as `APP_NET` but for background config.ed apps
* `WORK_PROFILE` (enabled|disabled) - set when work profile is toggled
* `MDATA` (enabled|disabled) - set when cellular data is toggled
* `ADB_WIFI` (enabled|disabled) - set when adb wifi (wireless adb) is toggled
* `WIFI_CONNECTED` (yes|no) - set when wifi has made a connection to an ssid
* `WAN_ACCESSIBLE` (yes|no) - yes when values/variables from the `Get Network Info` action confirms yes. (found in the `Wan Check Switch` task's 4th action)
* `HIGH_PING` (yes|no) - set when ping is stable/unstable based on the hardcoded ping response numbers in the `Ping Test` task
* `VPN_CONN` (yes|no) - set when any VPN connection is made 
* `APP_CTX_INVISIBLE_FG_NET_UNSET_PKG` (pkg name) - set when app is changed, not listed in app context & exists in the `/sdcard/Tasker/projects/app_ctx_invisible_apps_for_fg_unset_profile.txt` file
* `FG_NET_APP_NAME` (name of the foreground app opened) - name of the net needing foreground running app
* `FG_NET_APP_OPT` (<empty value>|nonet) - when `Fg Net Flags Set (Optional)` is active the user will not prompt the user for enabling wan. the var is cleared by the `Fg Net Flags Set (Optional)` profile's exit task. `nonet` value is just saying that the app doesn't need to have wan connectivity. 2 cases where this var is cleared.
    - stay out of the app for 15 seconds
    - wan is available within that 15 second window (`Fg Net Flags Set`, `Net Set (User)` & `Net Set` profiles)
* `SHIZUKU_RUNNING` (yes|no) - set yes when shizuku is running else set to no
* `ROOT_STAT` (su|nosu) - set to "su" if root is present, "nosu" if not & is checked at boot since it's running from `Check Set Stuff At Boot` profile & Task. Or this can be set from running the task itself
* `FG_OBD` (yes|no) - when set obd app is running in the foreground
* `BG_OBD` (yes|no) - same as `FG_OBD` but for background
* `BT_CONNECTED` (true|false) - toggled when a bluetooth connection is connected/disconnected
* `LOW_POWER_MODE` (enabled|disabled) - value is toggled when low power mode is toggled 
* `PHONE_LOCK_SET` (true|false) - value is evaluated when screen is off & toggled if phone lock is set (value set by test display action)
* `PHONE_LOCKED` (true|false) - toggled when phone is locked/unlocked
* `FG_MAP` (true|false) - value is toggled when a map app is or is not running in the foreground
* `BG_MAP` (true|false) - same as `FG_MAP` but for background
* `TTS_ENGINE_VOICE` (default:default|<user set value>|<cleared>) - run by the `TTS Test` task which if a TTS engine is set/configured will use the `default:default` value in the `Engine:Voice` arg of the `say` action (action 1 in the task). Set your working/preffered TTS value here. The `TTS Test` task is also called by the `Lazy workflow initializer` task which is run at boot.
* `WAKE_UP_NUM` (phone number) - stores the number of who needs to be called at a scheduled time set from automate's `Alarmy call` flow (mostly for when one is asked to be "woken up" & you're sound asleep)
* `WL_HOTSPOT` (enabled|disabled) - wifi hostspot state 
* `NET_SRC_TOGGLE_COUNT` - keeps a count of number of times WAN src was toggled which if exceeded (4 times) the workflow will prompt the user for if the toggling needs to continue to which if clicked yes will reset the counter back to 0 else will keep the WAN toggling profiles to inactive until ping state says "stable ping" which also resets the WAN switch counter to 0
* `TASKER_HELPER_FOR_BT_TOGGLE` - var to check if tasker's bluetooth toggle action works (on non-root devices) wherein the following values will be assigned to it at boot IF the TaskerSettings app is installed. "ui_only", "works", "err". If TaskerSettings app is not installed the var is assigned "not_installed". what each value means
    - ui_only - just toggles the ui. does not affect the setting
    - works - says that the helper works as expected  
    - err - the action errored. you will have to see for yourself what kind of error (for me it was yapping about permissions even though it worked previously...must be another droid update)
    - not_installed - literally what it says
* `TASKER_HELPER_FOR_DATA_TOGGLE` - same as `TASKER_HELPER_FOR_BT_TOGGLE` but for data action
* `TASKER_HELPER_FOR_SENSOR_TOGGLE` - same as `TASKER_HELPER_FOR_BT_TOGGLE` but for toggling sensors using taskersettings via shell `service call sensor_privacy 9 i32 [1|0]`

### Automate Flows & what they do 
* `Tasker - Check Set boot stuff.flo` - sets variables, settings, flags & such after boot. Basically an initializer helper of sorts for tasker
* `Tasker - settings & states checks.flo` - contains 3 flow beginnings
    - `Cellular Data Stat` - sets the `MDATA` global var to "enabled"|"disabled" when cellular data state has been changed sending a custom broadcast (`net.dinglisch.tasker.CELLULAR_DATA`) to the profile `Mobile Data Var Set via Automate`
    - `Sim Presence` - sends a custom `android.intent.action.SIM_PRESENT` broadcast to the `(Un)Set Sim Presence Airplane Mode via Automate` profile to set airplane mode based the `%SIM_STATE` var which can't be used in the variable state context in a profile
    - `Hotspot State` - sets the `WL_HOTSPOT` global var to "enabled"|"disabled" when wifi hotspot has been changed by sending a custom broadcast (`net.dinglisch.tasker.WL_HOTSPOT`) to the `Wifi Hotspot State Via Automate` profile
* `Tasker - Airplane boarding.flo` - flow to disable all phone settings that need to disabled on a flight
* `Tasker - Alarmy call.flo` - pick a contact from popup, set a time for the fibre to be paused for, which then sends a broadcast to the `Custom Intent Recvr` profile in tasker enabling `Alarmy Call` &  `Alarmy Call Disable` profiles



### This is the ordering in which I keep the profiles in tasker to make maintainability & sensibility RELATIVELY easier than the mixup order you get when you import the project & unfortunately there's no option to preserve the profile ordering

    Check Set Stuff At Boot
    New App Installed
    Custom Intent Recvr
    Tasker Log Clear
    Disable Profiles
    ======== Global Var Setters ========
    Phone Lock State
    Phone Unlock State
    Low Power State
    Shizuku Var Set
    Adb Wifi State Var Set
    VPN state
    Bluetooth Connection Check
    wifi var set
    Wifi Hotspot State Via Automate
    Wifi Hotspot State
    Work Profile Set
    App Context Invisible Fg Apps
    Mobile Data Var Set
    Mobile Data Var Set via Automate
    (Un)Set Sim Presence Airplane Mode
    (Un)Set Sim Presence Airplane Mode via Automate
    ========= Low Power Mgr =========
    screen Off Low Power
    screen On Locked
    screen On No Lock No Low Power
    screen Unlocked No Low Power
    Charging No Low Power
    ============ Net Mgr ===========
    Fg Net Flags Set (Optional)
    Fg Net Flags Set
    Fg Net Flags Unset
    Bg Net (Un)Set
    Wifi Hotspot Autoset Stuff
    Usb Tether Autoset Stuff
    Redundant Net Src
    Net Set (User)
    Net Set
    Ping Test
    Wan Check Switch
    Net Src Disconnected Switch
    Net Unset
    No Root Screen Off Data Disconnected
    ============ Map Set ============
    Map Fg Flags (Un)Set
    Map Bg Flags (Un)Set
    Map (Un)Set
    =========== Bluetooth Stuff ========
    OBD2 Fg Flags (Un)Set
    OBD2 Bg Flags (Un)Set
    Bluetooth Set
    Bluetooth Unset
    No Root Screen Off BT Disconnected
    ============= Others =============
    Automate Flow Retrigger
    Email Auto Sync (Un)Set
    Alarmy Call
    Alarmy Call Disable
    Gaming
    Toggle Dev Mode Per App
    Clipboard Link Sanitizer
    Alarm Vol Auto Set
    Autoread Whatsapp
    Airplane Boarding
    Bottom "Buffer" Profile. Not To Be Used
