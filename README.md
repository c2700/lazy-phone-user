# lazy-phone-user
tasker project for the average lazy user. This needs llamalab automate, auto input, auto notification. There are Tasks that have are specific to doing one thing which could've been a block but no thanks to the android drama going on, I've had to set some checks for sdk level < 34, root & or shizuku being there which if the conditions are satisfied would be one action block for things that require root/shizuku else it would either be an entire user input simulation or nothing at all with just a tts/toast msg depending on the display's state (lock set/unset)

## setup
1. import the automate flo's into automate
2. set entry apps you always need WAN connectivity for in the `Fg Net Flags Set` profile (any & every app that you deem needing net including maps, games, browsers, ftp clients etc. goes here).
3. at the `Fg Net Flags UnSet` (with the invert option selected if you recreate the profile's app trigger) set the `mobile data settings` activity or the settings app itself (this is an addition to not trigger the profile when the `Mobile Data Settings` activity is open to enable the data connectivity) & any other apps/activity you need the the WAN connectivity trigger to not pickup on while being in that app/activiyt. idea is something along the lines of "need wan connectivity for f-droid to download stuff (which is already downloading) & all of a sudden get a call & the call activity takes the foreground over f-droid's activity & you don't want that wan connectivity to be disturbed while the call activity is in the foreground & until the download is complete. I have set only play store & whatsapp on both activities & added some settings activities that the app profile trigger showed in the `Fg Net Flags UnSet` profile to make it more easier to set up
4. set your apps (for notification) in the `Bg Net (Un)Set` profile's auto notification event..idea is to set the WAN connectivity enabled for those apps you would want the network to be left enabled until done, like downloads
5. set your entry games in the `Gaming` profile
6. set the map apps you use in the `Map Bg Flags (Un)Set` profile
7. Open up `Toggle Data` task & replace the actions in the Autoinput Action (the configuration argument) at action number 15 with whatever actions you need to perform to get cellular data enabled from the `Mobile Data settings` activity. (the activity, button placement, UI differs from android falvour to flavour)
8. (optional if this is not needed) remove/disable the `Work Profile Set` profile & if needed/wanted remove the WORK_PROFILE global var from all other actions, conditions & profiles. The action for it is created the way it is for when corporate apps are installed onto a work profile & one needs to show their activity as online at all times or needs to be responsive & needs connectivity at all times as long as there's some source of connectivity & for as long as the work profile's active.
9. `Gaming Clock` & `Regular CPU Usage`. Reconfigure the cpu list with their respective low & high clocks & governors configured. (I have set my `Gaming Clock` task to the highest that was available with all core governors to performance & the `Regular CPU Usage` all to schedutil (those were the only governors available for both my phone's kernels).
10. `(Un)Set Sim Presence Airplane Mode` or `(Un)Set Sim Presence Airplane Mode via Automate` profiles. Enable/delete ONLY ONE of since they do the same thing (toggle airplane mode on sim presence) with the only difference being the Action in the `intent received` event from both profiles being
    - android's built-in `SIM_STATE_CHANGED` broadcast is said to be unreliable since android 14 (https://issuetracker.google.com/issues/302614301). I haven't faced that issue on android 13
    - `SIM_PRESENT` is a custom broadcast sent from automate to tasker & this flo needs to be left running 
11. enable/delete ONLY ONE of `Mobile Data Var Set via Automate` or `Mobile Data Var Set`.
    - `Mobile Data Var Set` - do a `find setting` after toggling the data in the profile so it gets you the right state of your cellular data
    - `Mobile Data Var Set via Automate` will need the `Tasker - Cellular Data state check` flo from automate to be left running
12. (disable/delete if not needed which you would need to do to the `OBD2 (Un)Set` profile as well) set your obd apps as the entry app in `OBD2 Fg Flags (Un)Set` profile & the very same obd apps as auto notification entry in `OBD2 Bg Flags (Un)Set` profile
13. At `Ping Test` task you might want to make changes in the numbers at action (conditional actually) 14 & 18 if you think the numbers don't apply to your use case
15. you may need to change the `Data` field in the `Send Intent` action in tasker which are intended to invoke Automate flos (which will be indicated with the label in the action), if the "Flow URI" field in the imported automate flo's start block changes, to whatever the content has been changed to in automate's start block.
14. if you do not want the toasts from the `Ping test` & `WAN check` the actions to disable/delete are the following
15. run the `Check Set Stuff At Boot` TASK to initialize global vars & run the needed automate flo's based on the selected profiles

     - `Ping Test` Task
        - action 1
        - action 16
        - action 20

     - `WAN Check` Task
        - action 1
        - action 22
        - action 37


## What each Profiles do
1. `Check Set Stuff At Boot` - after boot, initializes stuff like setting global vars & enables/disables profiles as needed for the workflow to work right
2. `wifi var set` - profile to set mobile data stats as global variable(s)
3. `Mobile Data Var Set` - profile to set mobile data stats as global variable(s)
4. `Mobile Data Var Set via Automate` - profile to set mobile data stats as global variable(s) through automate
5. `Shizuku Var Set` - profile to set Shizuku Var Set stats as global variable
6. `Adb Wifi State Var Set` - profile to set Adb Wifi State stats as global variable
7. `Fg Net Flags Set` - profile to set global variable(s) to enable wan connectivity
8. `Fg Net Flags Unset` - profile to set stats as global variable(s) to disable wan connectivity
9. `Bg Net (Un)Set` - profile to set background/notification app stats as global variable(s) to toggle wan settings
10. `Map Fg Flags (Un)Set` - profile to set stats as global variable(s) to toggle settings for maps in foreground
11. `Map Bg Flags (Un)Set` - profile to set stats as global variable(s) to toggle settings for maps in background
12. `OBD2 Bg Flags (Un)Set` - profile to set stats as global variable(s) to toggle settings for set obd2 apps running in foreground
13. `OBD2 Fg Flags (Un)Set` - profile to set stats as global variable(s) to toggle settings for set obd2 apps running in background
14. `VPN state` - profile to set vpn stat (enable/disable) as global variable
15. `(Un)Set Sim Presence Airplane Mode` - toggle airplane mode when sim inserted/ejected
16. `(Un)Set Sim Presence Airplane Mode via Automate` - toggle airplane mode when sim inserted/ejected (broadcast sent by automate. keep this flo running in automate if this profile's to be used)
17. `Airplane Boarding` - settings to toggle when boarding an airplane (run `Tasker - Airplane Mode` flo from automate)
18. `Alarmy Call` - use this if one asked you to call them to be woken up. run the `Tasker - Alarmy Call` flo from automate to set the number/contact
19. `Gaming` - cpu state toggles when enter/opening games
20. `screen Off Low Power` - set low power mode when screen is off (toggles the other screen based low power profiles if a lock is set on the phone)
21. `screen On Locked` - profile when phone is locked but screen is up (this may possibe)
22. `screen Unlocked No Low Power` - disable all low power settings when unlocked
23. `screen On No Low Power` - disable all low power settings when screen is on (no lock is set)
24. `Clipboard Link Sanitizer` - takes away some strings from the clipboard....takes away the `?sid.*` when link is copied to clipboard
25. `Alarm Vol Auto Set` - set all alarm vol to max when an alarm's about to ring
26. `Autoread Whatsapp` - literally what it says (except set your own interval if you need to)
27. `Wifi Hotspot Autoset Stuff` - when hotspot is set, disables all wan related profiles (in case one wants to change stuff at will)
28. `Usb Tether Autoset Stuff` - when usb tether is set, disables all wan related profiles (in case one wants to change stuff at will)
29. `Net Set (User)` - disables all wan managing profiles when user enables any wan connectivity while no wan managing profiles are active
30. `Net Set` - enable wan connectivity
31. `Net Unset` - disable wan connectivity (there's a few more conditions than what's in the `Net Set` profile)
32. `Net Src Disconnected Switch` - switch to data when wifi is on but is disconnected
33. `Redundant Net Src` - when data is enabled & wifi is on. disable data if wifi is connected & vice versa
34. `Wan Check Switch` - checks if wan is reachable when connected via wan iface, which then waits for `Ping Test` to set HIGH_PING global var & based on the var's value changes or leaves it as is
35. `Ping Test` - constantly checks the ping state (5 pkts)
36. `Map (Un)Set` - toggle map app settings
37. `Intent Recvr` - broadcast receiver for anything else from anywhere else neededf (automate for this case like setting global vars)
38. `Work Profile Set` - toggle settings, var.s & values for work profiles
39. `Bluetooth Set` - profile to enable bluetooth
40. `Bluetooth Connection Check` - trigger to get connection/disconnection event
41. `Bluetooth Unset` - profile to disable bluetooth

### There maybe slight delays in running tasks triggered by the low power mode profiles which send intents to llamalab automate for weaker phones

