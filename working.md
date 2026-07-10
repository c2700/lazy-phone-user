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
* `Net Set` - toggle wan connectivity based on global var.s values set from `Fg Net Flags Set`, `Fg Net Flags Unset`, `Bg Net (Un)Set`, `Work Profile Set` or `VPN state` profiles. if value is enabled, WAN connectivity is enabled, if disabled then WAN connectivity is disabled. (this is where the WAN connectivity toggling happens)
* `Net Set (User)` - disables all wan TOGGLING/IFACE SWITCHING PROFILES when user enables any wan connectivity while no wan managing profiles are active or have performed actions that DON'T trigger said wan managing profiles to be active (`Net Set`, `Wan Check Switch`, `Ping Test` ,`Net Unset`, `Net Src Disconnected Switch`). enables said disabled profiles when this profile is inactive. this for when user wants to manage WAN connectivity
* `Net Src Disconnected Switch` - switch to data when wifi gets disconnected when a net needing app is running (foreground/background)
* `Redundant Net Src` - when data & wifi are enabled & wifi is connected, disable data. vice versa is also applicable. when no sim, wait for 20 seconds until wifi connection is made else disable wifi
* `Wan Check Switch` - checks if wan is reachable when connected via enabled/active/connected wan iface, which then waits for `Ping Test` to set `HIGH_PING` global var & based on the var's value shows an input dialog to change the ssid to which if user selects "no" prompts to change to cellular data to which if user selects "no" then leaves the iface as is by stopping the profile assigned task. switch WAN src based on ping numbers
* `Ping Test` - checks ping timings (5 pkts) in an infinite loop (goto step 1 is applied)
* `Net Unset` - disable wan connectivity. Active when ALL wan connectivity profiles (`Fg Net Flags Set`, `Fg Net Flags Unset`, `Bg Net (Un)Set`, `VPN state` & `Work Profile Set`) have set their global var.s to satisfy the conditions to set this profile active to disable all wan connectivity
* `No Root Screen Off Data Enabled` - Task that tells the user to disable data every 10 seconds when screen is locked/off & is not in use until data is disabled (active when root is unavailable or shizuku is not running)

##### 5. <b>Map settings</b>
* `Map Fg Flags (Un)Set` - set global variable(s) to be used to toggle settings for maps in foreground <b>(just flag setting for `Map (Un)Set` profile)</b>
* `Map Bg Flags (Un)Set` - same purpose as `Map Fg Flags (Un)Set` but for apps in background <b>(just flag setting for `Map (Un)Set` profile)</b>
* `Map (Un)Set` - toggle map app settings

##### 6. <b>Bluetooth toggle</b>
* `OBD2 Bg Flags (Un)Set` - set stats for global variable(s) to be used to toggle settings for set obd2 apps running in background <b>(just flag setting for `Bluetooth Set` profile)</b>
* `OBD2 Fg Flags (Un)Set` - same purpose as `OBD2 Bg Flags (Un)Set` but for when the apps running in the foreground <b>(just flag setting for `Bluetooth Set` profile)</b>
* `Bluetooth Set` - to enable bluetooth (dependent on the global var.s set by obd2 & bluetooth check profiles)
* `Bluetooth Unset` - to disable bluetooth (dependent on the global var.s set by obd2 & bluetooth check profiles)
* `No Root Screen Off BT Disconnected` - same as `No Root Screen Off Data Enabled` but for bluetooth

##### 7. <b>low power based on lock & screen state</b>
* `screen Off Low Power` - set low power mode when screen is off. `Test Display` action to set `PHONE_LOCKED` & `PHONE_LOCK_SET` values & switch bluetooth off if connections are 0
* `screen On Locked` - when phone is locked but screen is up
* `screen On No Lock No Low Power` - disable all low power settings when screen is on & no lock is set
* `screen Unlocked No Low Power` - disable all low power settings when unlocked
* `Charging No Low Power` - when power source is plugged in, disable power save mode

##### 8. <b> Stuff for tasker & workflow </b>
* `Tasker Log Clear` - literally what it says.
* `New App Installed` - to prompt the user if an installed app is to be set in the app context of the profiles in this project to which if pressed yes, will open tasker. will only be a popup if already in tasker
* `Toggle Profiles` - disable those profiles used for separation, profiles that require root & mass toggels Battery Audio related profiles based on the state of `Battery Audio Toggle` profile.
* `App Context Invisible Fg Apps` - to get app/pkg names that are not listed in tasker's app context & compare the pkg name against pkgs set in `/sdcard/Tasker/projects/app_ctx_invisible_apps_for_fg_unset_profile.txt` (too long a name I know) file which if it's there sets the `APP_CTX_INVISIBLE_FG_NET_UNSET_PKG` global var (yes, it's a mouthful & too wordy...best I could come up with) with that pkg name via grep (done like this "grep <pkg name> | grep -iv '#'"). The global var is then used in the `Fg Net Flags Unset` profile. can be used to set/unset global vars & files besides what's being currently used
* `Get Recents` - a profile to see if the user's on the "recents" screen just so the fg net app profile's timeout can be paused when in the recents screen


##### 9. <b>other stuff</b>
* `Sanitized URL share` - literally what it says. for now only following links are "sanitization" 
    - youtube share links matching `?sid.*`
    - links with google references 
    - instagram links matching `\/\?igsh=.*`
* `Email Auto Sync (Un)Set` - sets autosync when email client is in foreground, unsets when not in foreground for more than one minute
* `Alarm Vol Auto Set` - set all alarm vol to max when an alarm's about to ring
* `Autoread Whatsapp` - literally what it says (except set your own interval if you need to)
* `Alarmy Call` - to call one at a scheduled time if you forget to (I use this when one asks me to wake them up from a phone call). This profile is toggled by (enabling & disabling the profile & setting the phone number as a global var for the profile to use)  run `Tasker - Alarmy call.flo` from automate to set the number/contact
* `Alarmy Call Disable` - disables `Alarmy Call` profile (and that the `Alarmy Call` also needing to be enabled is also a cndition) when the incoming caller is the same one set in `WAKE_UP_NUM` by the `Tasker - Alarmy Call.flo` flow causing the task to go inactive, which is why I set the action to disable itself
* `Gaming` - cpu state toggles when enter/opening games
* `Airplane Boarding` - settings to toggle when boarding/deboarding an airplane. keep the `Tasker - Airplane boarding.flo` running when enabling this profile
* `Toggle Dev Mode Per App` - disable dev mode when entering apps that complain about it, enable when exiting with "show touches" & adb enabled
* `Flashlight State Based Autobrightness` - toggle Flashlight & autobrightness will be toggled for 2 seconds


##### 10. <b>not as a automation usage but something cosmetic. Play audios based on battery levels & power source plug events</b>
* <b>Automate flow beginnings from the below profiles will be run from the `Tasker - Battery noise` flow (which is `(Template) Tasker - Battery noise.flo` in the repo) </b>
* `Shutdown Audio` - play audios at randomly selected from a list of audio files set by the user at shutdown event
* `Unplugged 1 Pcent Battery` - play audio at loop when at 1 percent battery
* `Unplugged 2 Pcent Battery` - same as `Unplugged 1 Pcent Battery` but at 2 percent battery
* `Battery Audios` - run the `Battery State` flow beginning when battery's between 3% & 99%. different audios at different battery levels when charger's plugged, pulled, charging & discharging (entry & exit at a battery range)
* `Battery Full Audio Event` - runs a one time set audio set by the user in the profile's `Music Play` & run the `Plugged in Battery full`
* `Power Connected` - at less than 3 percent battery, on plugging power source run a one time audio set by the user. if at 100% battery run the `Plugged in battery full` from flows
* `Power Disconnected` - at less than 3 percent battery, on removing power source rus an audio set by the user in an infinite loop until power plugged in or when greater than 3%. if at 100% battery run the `Plugged in battery full` flow
* `Battery Audio Toggle` - any vol/audio stream set to 0 the, the aforementioned battery audio profiles will be disabled & flows will be stopped. exit task enables the profiles which in turns runs the relevant flows


##### 11. <b>not as a automation usage but just for tasker interactivity convenience</b>
* `Bottom "Buffer" Profile. Not To Be Used` - literally what it says. except I originally intended to use it as some sort "buffer" so that I could move the profiles around cuz profiles that were last in the task list I had a tough time getting it to move around. now that the AI button's been added which overlays itself on the profile toggle button (the ones that are last in the list), that "buffer" profile (still used as buffer) is also now a profile which will be last in the list who's toggle will remain obstructed instead of the one's that are of use to the user. (TLDR: just a UI inconvenience mitigator). or you could disable the "AI Generation Button Enabled" option by going to the 3 dots on top of the main activity -> UI tab -> "AI Generation Button Enabled".
* the rest of the profiles (mostly being ones containing `=` & words in it like `Net Mgr`) are just profile separators. If enabled `Toggle Profiles` is set to disable them, including the `Bottom "Buffer" Profile. Not To Be Used` profile


### custom global vars set & used by profiles & tasks from the above group of profiles
1. `APP_CTX_INVISIBLE_FG_NET_UNSET_PKG`, `PHONE_LOCK_SET`, `NET_SRC_TOGGLE_COUNT`, `APP_NET`, `BG_NET`, `WORK_PROFILE`, `MDATA`, `ADB_WIFI`, `WIFI_CONNECTED`, `WAN_ACCESSIBLE`, `VPN_CONN`, `FG_NET_APP_OPT`, `FG_NET_APP_NAME`, `ROOT_STAT`, `SHIZUKU_RUNNING`, `HIGH_PING` - [network toggle & check profiles](#global-var-setter-profiles-for-phone-settings), [low power & screen lock profiles](#low-power-based-on-lock-&-screen-state) & [global var setter profiles for phone settings](#global-var-setter-profiles-for-phone-settings)
2. `BG_OBD`,`FG_OBD`,`BT_CONNECTED`,  `ROOT_STAT`, `SHIZUKU_RUNNING` - [bluetooth toggle profiles](#bluetooth-toggle) & [global var setter profiles for phone settings](#global-var-setter-profiles-for-phone-settings)
3. `LOW_POWER_MODE`, `PHONE_LOCKED`, `ROOT_STAT` - [low power & screen lock profiles](#low-power-based-on-lock-&-screen-state) & [global var setter profiles for phone settings](#global-var-setter-profiles-for-phone-settings)
4. `BG_MAP`, `FG_MAP` - [Map settings toggling profiles](#map-settings)
5. `WAKE_UP_NUM` - [other stuff](#other-stuff)

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
* `AUTOMATE_CONTENT_BASE_URI` (content://com.llabalab.automate.provider/flows) - stores only the content provider authority which is used across all other `AUTOMATE_` global var.s which are used as content uri's pointing to automate flows.

## What each Tasker profiles do
#### Tasks who's Description's given in the profile section with the same name as the task
`Wan Check Switch`, `Ping Test`, `Net Set`, `Custom Intent`, `Autoread Whatsapp`, `(Un)Set Sim Presence Airplane Mode`, `Sanitized URL Share`, `Bluetooth Unset`, `Redundant Net Src Switch`, `Alarmy Call`
* Toggle Profiles, Toggle Data, Toggle Bluetooth, Unset All Alarms - literally what their task names say
* Gaming Clock,Regular Cpu Clock,Low Clock Cpu - sets the clocks to whatever the user counts/sets as regular, low or performance clocks 
* Restart Flow - stop a flow, then start from a flow beginning (no way to stop a flow beginning)
* Fg Map Actions - actions to perform on map apps
* All Vol Set - task to set a vol lvl to all audio streams
* Lazy Workflow Initializer - Action 33 is just what I think would work best, action 34 is left as disabled in case the user wants to have their desired profiles enabled & automate flows left running. Rest of the description is at the `Check Set Stuff At Boot` profile
* New App Installed - to prompt the user if the installed app has to be added to the work IF the app requests for permissions checked by the `Permission Check` task
* Net Src Switch - just switches between data & wifi
* Enable Low Power Mode - disables dev sensors (run by `Toggle Dev Mode sensors` task), bluetooth, wifi, data, location & autosync where possible, enables low power mode & airplane mode if no sim
* Disable Low Power Mode - enables dev sensors (run by `Toggle Dev Mode sensors` task), disables low power mode & airplane mode if sim is present
* TTS Test - to test if a tts can be used (the say action)
* Permission Check - checks if a requested app is asking for wan, location & or bluetooth connectivity (called by `New App Installed`)
* Default Xor Profiles - runs the `Sim Presence` & `Cellular Data Stat` flow beginnings from `Tasker - settings & states checks.flo` flow, enables 
* Profile to Flow Runner - enable profiles & run automate tasks that those enabled profiles depend on 
* Screen State Based Msg - msg is posted via tts or a toast depending screen or tts state
* Screen & Lock State Low Power Profile Toggle - toggles the low power profiles based on the phone screen lock being set 
* Delayed Autobrightness Toggle - description in `Flashlight State Based Autobrightness` profile
* Random Audio Play - Task to randomly play from a list of audio files fed to it as parmeter
* Toggle Dev Mode sensors - task for toggleing the `disable sensors` dev options qs toggle (won't run without root/shizuku/adb or if tasker helper stat ). the number 9 in the shell actions 3, 6, 7, 11, 14, 15 differs between android versions. [stackexchange reference](#https://android.stackexchange.com/questions/246299/can-sensors-off-developer-options-be-controlled-via-adb)

### Automate Flows & what they do 
* `Tasker - Check Set boot stuff.flo` - sets variables, settings, flags & such after boot. Basically an initializer helper of sorts for tasker
* `Tasker - Airplane boarding.flo` - flow to disable all phone settings that need to disabled on a flight
* `Tasker - Alarmy call.flo` - pick a contact from popup, set a time for the fibre to be paused for, which then sends a broadcast to the `Custom Intent Recvr` profile in tasker enabling `Alarmy Call` &  `Alarmy Call Disable` profiles
* `(Template) Tasker - Battery noise.flo` - plays audio files set by the user at set battery levels. Also invoked by tasker profiles.
* `Tasker - settings & states checks.flo` - contains 4 flow beginnings & also sends a broadcast to the `Custom Intent Recvr` profile to disable respective profiles
    - `Cellular Data Stat` - sets the `MDATA` global var to "enabled"|"disabled" on cellular data state change sending a custom broadcast (`net.dinglisch.tasker.CELLULAR_DATA`) to the profile `Mobile Data Var Set via Automate`. Disables  `Mobile Data Var Set` on 1st run
    - `Sim Presence` - sends a custom `android.intent.action.SIM_PRESENT` broadcast to the `(Un)Set Sim Presence Airplane Mode via Automate` profile to set airplane mode based the `%SIM_STATE` var which can't be used in the variable state context in a profile. Disables `(Un)Set Sim Presence Airplane Mode` profile on 1st run
    - `Hotspot State` - sets the `WL_HOTSPOT` global var to "enabled"|"disabled". Disables `Wifi Hotspot State` profile on 1st run
    - `Wifi State` - sets `WIFI_CONNECTED` on wifi connection state. Disables `wifi var set` profile on 1st run

### Easer Setup:
<b>(event & profile names literally do what the names imply)</b>
- Script tab
    - bluetooth disable: connect `bluetooth disable broadcast recv` event with `bluetooth disable` profile
    - bluetooth enable: connect `bluetooth enable broadcast recv` event with `bluetooth enable` profile

- Event tab: 
    - bluetooth disable broadcast recv - recieve BT_DISABLE action to trigger profile
    - bluetooth enable broadcast recv - recieve BT_ENABLE action to trigger profile 

- Profile:
    - bluetooth disable: task to disable bluetoot
    - bluetooth enable: task to enable bluetooth

### This is the ordering in which I keep the profiles in tasker to make maintainability & sensibility RELATIVELY easier than the mixup order you get when you import the project & unfortunately there's no option to preserve the profile ordering

    Check Set Stuff At Boot
    New App Installed
    Custom Intent Recvr
    Tasker Log Clear
    Toggle Profiles
    ======== Global Var Setters ========
    Phone Lock State
    Phone Unlock State
    Low Power State
    Shizuku Var Set
    Adb Wifi State Var Set
    VPN state
    Bluetooth Connection Check
    wifi var set
    Wifi Hotspot State
    Work Profile Set
    Get Recents
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
    Hotspot Autoset Stuff
    Redundant Net Src
    Net Set (User)
    Net Set
    Ping Test
    Wan Check Switch
    Net Src Disconnected Switch
    Net Unset
    No Root Screen Off Data Enabled
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
    ========= Battery Audios ==========
    Shutdown Audio
    Unplugged 1 Pcent Battery
    Unplugged 2 Pcent Battery
    Battery Audios
    Battery Full Audio
    Battery Audio Toggle
    Power Connected
    Power Disconnected
    ============= Others =============
    Email Auto Sync (Un)Set
    Alarmy Call
    Alarmy Call Disable
    Gaming
    Toggle Dev Mode Per App
    Sanitized URL share
    Alarm Vol Auto Set
    Autoread Whatsapp
    Airplane Boarding
    Flashlight State Based Autobrightness
    Bottom "Buffer" Profile. Not To Be Used


