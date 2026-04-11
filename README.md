# lazy-phone-user
Tasker project with llamalab automate flows for the average lazy phone user. works on rooted & non-rooted phones. 

TLDR: toggle/auto-setting wan connectivity/maps relevant settings/low power & airplane modes/cpu settings/bluetooth/volumes/tethering settings/obd2 based on set apps/notifications/global vars.s/conditions/settings.


### Required apps
 1. Tasker
 2. llamalab automate
 3. autoinput
 4. auton otification. 

# disclaimers:
1. There maybe slight delays in running tasks triggered by the low power mode profiles which send intents to llamalab automate for weaker phones.
2. if at times actions/tasks don't run, try performing said action/task from the `run an action` option from tasker just to see if the action's being run which if it doesn't, try one few or all of the below
    - reboot
    - disable tasker, reboot & `run an action` as a test which if it does work then enable tasker
3. if certain profile's actions/tasks aren't triggered, try performing actions/op.s that gets the profile inactive & back active just to get the task/actions re-triggered
4. There are tasks that are specific to doing one thing which could've been just one action but no thanks to the android drama going on, I've had to set some checks for sdk level < 34, root & or shizuku being there which if the conditions are satisfied would be one action for things that require root/shizuku else it would either be an entire user input simulation through autoinput or no action at all with just a tts/toast msg saying "x is left enabled/disabled" depending on the display's state (lock set/unset)

# setup
#### list of profiles to be mututally exclusively enabled to avoid unexpected behaviours or clash in tasks
1. `Mobile Data Var Set via Automate` & `Mobile Data Var Set`
2. `(Un)Set Sim Presence Airplane Mode via Automate` & `(Un)Set Sim Presence Airplane Mode`

## <b>Configuration</b>
import the automate flows (the `.flo` files from the `Automate flows` directory) into automate & the taskert project `Lazy_User.prj.xml` into Tasker
#### <b>tasker</b>
1. set your entry apps you always need WAN connectivity for when in foreground in the `Fg Net Flags Set` profile (ANY & EVERY app that you deem needing net including maps, games, browsers, ftp clients etc. goes here).
2. `Fg Net Flags UnSet` - recreate the app context with the invert option selected & set `mobile data settings` app or the `settings` app itself & any other apps you DON'T want this profile's app context to be pickuped up on while being in that app/activity. Idea is something along the lines of "need wan connectivity for f-droid to download stuff (which is already downloading) & all of a sudden get a call & the call activity takes the foreground over f-droid's activity & you don't want that wan connectivity to be disturbed while the call activity is in the foreground & until the download is complete. I have set only play store & whatsapp on both activities & added some settings activities that the app profile trigger showed in the `Fg Net Flags UnSet` profile to make it more easier to set up
3. set your apps (for notification) in the `Bg Net (Un)Set` profile's auto notification event...idea is to set WAN connectivity enabled for those apps you would want the network to be left enabled until done, like downloads
4. set your games in the `Gaming` profile's app context
5. set the map apps you use in the `Map Bg Flags (Un)Set` profile's app context
6. Open up `Toggle Data` task & replace the actions in the Autoinput Action (the configuration argument) at action number 15 with whatever actions you need to perform to get cellular data enabled from the `Mobile Data settings` activity. (the activity, button placement, UI differs from android falvour to flavour)
7. (optional if this is not needed) remove/disable the `Work Profile Set` profile & if needed/wanted remove the WORK_PROFILE global var from all other actions, conditions & profiles. The action for it is created the way it is for when corporate apps are installed onto a work profile & one needs to show their activity as online at all times or needs to be responsive & needs connectivity at all times as long as there's some source of connectivity & for as long as the work profile's active.
8. `Gaming Clock` & `Regular CPU Usage`. Reconfigure the cpu list with their respective low & high clocks & governors configured. (I have set my `Gaming Clock` task to the highest that was available with all core governors to performance & the `Regular CPU Usage` all to schedutil (those were the only governors available for both my phone's kernels). you're gonna need to delete/add some cpu actions depending on your phone's hardware
9. `(Un)Set Sim Presence Airplane Mode` or `(Un)Set Sim Presence Airplane Mode via Automate` profiles to be mutually exclusively enabled. The only difference between the 2 profiles is that the Action in the `intent received` event from both profiles are
    - android's built-in `SIM_STATE_CHANGED` (used by `(Un)Set Sim Presence Airplane Mode`) broadcast is said to be unreliable since android 13 (https://issuetracker.google.com/issues/302614301). I haven't faced that issue on android 13. Only "weird" thing is that I've been seeing that profile being triggered with that broadcast many times even when I haven't ejected/inserted the sim card
    - `SIM_PRESENT` (used by `(Un)Set Sim Presence Airplane Mode via Automate`) is a custom broadcast sent from automate to tasker & the `Tasker - Airplane mode.flo` needs to be left running for this profile & it's task to work as expected
10. `Mobile Data Var Set via Automate` or `Mobile Data Var Set` (to be mutually exclusively enabled)
    - `Mobile Data Var Set` - do a `find setting` (the magnifying glass next to the `Name` field) in the profile's `custom setting` context, toggle cellular data & you got the custom setting name that gets you the cellular data state filled in the Name field
    - `Mobile Data Var Set via Automate` will need the `Tasker - Cellular Data state check.flo` to be left running
11. (disable/delete if not needed which you would need to do to the `OBD2 (Un)Set` profile as well) set your obd apps as the entry app in `OBD2 Fg Flags (Un)Set` profile & the very same obd apps as auto notification entry in `OBD2 Bg Flags (Un)Set` profile
12. At `Ping Test` task you might want to make changes in the numbers at action 14 & 18 (conditional actually) if you think the ping numbers don't apply to your use case
13. you may need to change the `Data` field in the `Send Intent` action in all tasker tasks which are intended to invoke Automate flows (the flow to be invoked will be indicated in the `send intent` action's label field) to whatever the content has been changed to in the flow's start block. do this IF the "Flow URI" field in the imported automate flow's start block differs from what's there in tasker's `send intent` action's Data field.
14. reboot the phone or run the `Check Set Stuff At Boot` TASK to initialize global vars & run the needed automate flo's based on the selected profiles
15. if you do not want the toasts from the `Ping test` & `WAN check` the actions to disable/delete are the following

## What each Tasker profiles do
##### 1. <b>workflow base & entry point. stuff profiles here can be used to initiate/start the workflow & be used as a common entrypoint for implicit intents to trigger the profiles/tasks/actions</b>
* `Check Set Stuff At Boot` - after boot (basically unlock after boot), initializes stuff for the workflow, like setting global vars & enables/disables profiles as needed for the workflow to work right
* `Intent Recvr` - broadcast receiver for anything else from anywhere else needed (automate for this case like setting global vars)

##### 2. <b>global var setter profiles for phone settings</b>
* `wifi var set` - set mobile data stats as global variable(s) whenever wifi is toggled (used by the net management profiles)
* `Shizuku Var Set` - set Shizuku Var Set stats as global variable (used by profiles/tasks to check if it's running & then perform actions that require root/shizuku)
* `VPN state` - set vpn stat as global variable. sure I could have used `VPN_CONNECTED` but that var wasn't avaialable until I used the `VPN Connected` context for this profile
* `Adb Wifi State Var Set` - set Adb Wifi State stats as global variable (used by the net management profiles)
* `Mobile Data Var Set` - set mobile data stats as global variable(s) whenever data is toggled (used by the net management profiles)
* `Mobile Data Var Set via Automate` - set mobile data stats as global variable(s). (used by the net management profiles but triggered by intents sent from automate)
* `Bluetooth Connection Check` - trigger to get connection/disconnection event. wait for 15 seconds before disconnecting. the wait time is to wait for any other connections or for a reconnection

##### 3. <b>profile for airplane mode based on sim presence</b>
* `(Un)Set Sim Presence Airplane Mode` - toggle airplane mode when sim inserted/ejected. Uses android's `SIM_STATE_CHANGED` broadcast
* `(Un)Set Sim Presence Airplane Mode via Automate` - toggle airplane mode when sim inserted/ejected. custom broadcast `SIM_PRESENT` is sent by automate. keep `Tasker - Sim Check.flo` running when enabling this.

##### 4. <b>WAN iface toggle</b>
* `Work Profile Set` - toggle settings, var.s & values for your work profile
* `Fg Net Flags Set` -  set global variable(s) to enable wan connectivity for net requiring apps running in the foreground
* `Fg Net Flags Unset` - set stats as global variable(s) to disable wan connectivity for when net requiring apps are not running in the foreground. There's a delay of 14 seconds for the `Fg Net Flags Set` profile to be active which if it isn't the global var `%APP_NET` is to `no` which satisfies one of the `set variable` context's conditions from the `Net Unset` profile
* `Bg Net (Un)Set` - set/unset stats as global variable(s) to toggle WAN connectivity
* `Wifi Hotspot Autoset Stuff` - when hotspot is set, disables all wan related profiles (in case one wants to change stuff at will)
* `Usb Tether Autoset Stuff` - same as `Wifi Hotspot Autoset Stuff` but applicable for usb tethering
* `Net Set` - global var.s set from `Fg Net Flags Set`, `Fg Net Flags Unset`, `Bg Net (Un)Set`, `Work Profile Set` & `VPN state` profiles which if set to be enabled then Enable wan connectivity.
* `Net Set (User)` - when this profile active, disables all wan TOGGLING/IFACE SWITCHING profiles when user enables any wan connectivity while no wan managing profiles are active or have performed actions that DON'T trigger said wan managing profiles to be active (`Net Set`, `Wan Check Switch`, `Ping Test` ,`Net Unset`, `Net Src Disconnected Switch`). enables said disabled profiles when this profile inactive
* `Net Src Disconnected Switch` - switch to data when wifi gets disconnected
* `Redundant Net Src` - when data & wifi are enabled & wifi is connected, disable data. vice versa is also applicable
* `Wan Check Switch` - checks if wan is reachable when connected via enabled/active/connected wan iface, which then waits for `Ping Test` to set `HIGH_PING` global var & based on the var's value shows an input dialog to change the ssid to which if selected no prompts to change to cellular data to which if selected no then leaves the iface as is by stopping the profile assigned task.
* `Ping Test` - constantly checks ping timings (10 pkts)
* `Net Unset` - disable wan connectivity. Active when ALL wan connectivity profiles (`Fg Net Flags Set`, `Fg Net Flags Unset`, `Bg Net (Un)Set`, `VPN state` & `Work Profile Set`) have set their global var.s to those values that set this profile active to disable all wan connectivity

##### 5. <b>Map settings</b>
* `Map Fg Flags (Un)Set` - set global variable(s) to be used to toggle settings for maps in foreground
* `Map Bg Flags (Un)Set` - same purpose as `Map Fg Flags (Un)Set` but for when the apps are running in the background
* `Map (Un)Set` - toggle map app settings

##### 6. <b>Bluetooth toggle</b>
* `OBD2 Bg Flags (Un)Set` - set stats for global variable(s) to be used to toggle settings for set obd2 apps running in foreground
* `OBD2 Fg Flags (Un)Set` - same purpose as `OBD2 Bg Flags (Un)Set` but for when the apps running in the background
* `Bluetooth Set` - profile to enable bluetooth (dependent on the global var.s set byt obd13 & bluetooth check profiles)
* `Bluetooth Unset` - profile to disable bluetooth (dependent on the global var.s set byt obd14 & bluetooth check profiles)

##### 7. <b>low power based on lock & screen state</b>
* `screen Off Low Power` - set low power mode when screen is off. Mutually exclusive toggles the `screen On No Low Power` & `screen On Locked` profiles depending on the presence of a lock being set
* `screen On No Low Power` - disable all low power settings when screen is on (no lock is set)
* `screen On Locked` - when phone is locked but screen is up
* `screen Unlocked No Low Power` - disable all low power settings when unlocked
* `Charging No Low Power` - when power source is plugged in, disable power save mode

##### 8. <b>other stuff</b>
* `Clipboard Link Sanitizer` - literally what it says. for now only "sanitization" it does is on youtube share linke which is just taking away the strings matching `?sid.*`
* `Alarm Vol Auto Set` - set all alarm vol to max when an alarm's about to ring
* `Autoread Whatsapp` - literally what it says (except set your own interval if you need to)
* `Alarmy Call` - use this if one asked you to call them to be woken up. This profile is "managed" (enabling & disabling the profile & setting the phone number as a global var for the profile to use) by the `Tasker - Alarmy Call` flo from automate to set the number/contact
* `Gaming` - cpu state toggles when enter/opening games
* `Airplane Boarding` - settings to toggle when boarding/deboarding an airplane. keep the `Tasker - Airplane Mode` flo running when enabling this profile


9. <b>not as a automation usage but just for tasker interactivity convenience</b>
	* `Bottom "Buffer" Profile. Not To Be Used` - literally what it says. except I originally intended to use it as some sort "buffer" so that I could move the profiles around cuz profiles that were last in the list I had a tough time getting it to move around. now that the AI button's been added which which overlays itself on the profile toggle button (the ones that are last in the list), that "buffer" profile (still used as buffer) is also now a profile which will be last in the list who's toggle will remain obstructed instead of the one's that are of use to the user

### custom global vars set & used by profiles & tasks from the above group of profiles
1. `APP_NET`, `BG_NET`, `WORK_PROFILE`, `MDATA`, `ADB_WIFI`, `WIFI_CONNECTED`, `WAN_ACCESSIBLE`, `VPN_CONN`, `FG_NET_APP_NAME`, `ROOT_STAT`, SHIZUKU_RUNNING`, `HIGH_PING` - [network toggle & check profiles](#global-var-setter-profiles-for-phone-settings)
2. `BG_OBD`,`FG_OBD`,`BT_CONNECTED`,  `ROOT_STAT`, `SHIZUKU_RUNNING` - [bluetooth toggle profiles](#bluetooth-toggle)
3. `LOW_POWER_MODE`, `PHONE_LOCKED`, `ROOT_STAT` - [low power & screen lock profiles](#low-power-based-on-lock-&-screen-state)
4. `BG_MAP`, `FG_MAP` - [Map settings toggling profiles](#map-settings)
5. `WAKE_UP_NUM` - [other stuff](#other-stuff)

### when each of those global vars (more like flags) & what they mean
* `APP_NET` (yes|no) - value is toggled when a wan connection needing app is or is not in the foreground
* `BG_NET` (yes|no) - same as `APP_NET` but for background config.ed apps
* `WORK_PROFILE` (enabled|disabled) - set when work profile is toggled
* `MDATA` (enabled|disabled) - set when cellular data is toggled
* `ADB_WIFI` (enabled|disabled) - set when adb wifi (wireless adb) is toggled
* `WIFI_CONNECTED` (yes|no) - set when wifi has made a connection to an ssid
* `WAN_ACCESSIBLE` (yes|no) - yes when values/variables from the `Get Network Info` action confirms yet. (found in the `Wan Check Switch` task's 4th action)
* `HIGH_PING` (yes|no) - set when ping is stable/unstable based on the hardcoded numbers in the `Ping Test` task
* `VPN_CONN` (yes|no) - set when any VPN connection is made 
* `FG_NET_APP_NAME` (name of the foreground app opened) - name of the net needing foreground running app
* `SHIZUKU_RUNNING` (yes|no) - set yes when shizuku is running else set to no
* `ROOT_STAT` (su|nosu) - set to "su" if root is present, "nosu" if not & is checked at boot since it's running from `Check Set Stuff At Boot` profile & Task. Or this can be set from running the task itself
* `BG_OBD` (yes|no) - when set obd app is running in the foreground
* `FG_OBD` (yes|no) - same as `BG_OBD` but for background
* `BT_CONNECTED` (true|false) - toggled when a bluetooth connection is connected/disconnected
* `LOW_POWER_MODE` (enabled|disabled) - value is toggled when low power mode is toggled 
* `PHONE_LOCKED` (true|false) - toggled when phone is locked/unlocked
* `BG_MAP` (true|false) - value is toggled when a map app is or is not running in the foreground
* `FG_MAP` (true|false) - same as `FG_MAP` but for background
* `WAKE_UP_NUM` (phone number) - stores the number of who needs to be called at a scheduled time set from automate's `Alarmy call` flow (mostly for when one is asked to be "woken up" & you're sound asleep)
