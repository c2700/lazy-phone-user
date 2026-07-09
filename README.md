# lazy-phone-user
phone Automation workflow to make phone self-handled or self-managed. works on rooted & non-rooted phones (which works via UI simulation or via working/reliable helper apps or shizuku to do things that require higher privileges to do with one action block). Configure once & forget until maybe few more apps to be plugged in to relevant profiles

# TLDR of what this workflow does:
auto-toggle settings/automate user actions based on set apps/notifications/conditions/global var.s/settings which are normally done by the user. [working](working.md)

### TBD:
- [] local db that stores wifi, cell tower & gps info to check if one is at a familiar/saved location (all for checking if network interface toggling is to/can be done)
- [] on ending a navigation (maps), perform checks on the same db (all for checking if network interface toggling is to/can be done)
- [] prompt the user if the current location has to be added to the db
- [] optionally & possibly use wigle (minimal usage) where necessary (example: when the phone either can't pick up the full tower id or shows thats an incompletely id'ed physical cell id)

### Required apps
 1. llamalab automate
 2. Easer (solely for bluetooth toggling for phone's/roms that cannot interact with the bluetooth api's without higher priv.s by having tasker send intents to it)
 3. Tasker
 4. autoinput
 5. auto notification. 
 6. [TaskerSettings](https://github.com/joaomgcd/TaskerSettings) (api level check based actions & tasks are created based on what's written in this page)


# Disclaimers:
1. There maybe slight delays on weaker phones in running tasks triggered by the low power mode profiles which send intents to automate flows.
2. if at times actions/tasks don't run, try performing said action/task from the `run an action` option from tasker just to see if the action's being run which if it doesn't, try one few or all of the below
    - reboot
    - disable tasker, reboot & `run an action` as a test which if it does work then enable tasker
3. if certain profile's actions/tasks aren't triggered, try performing actions/op.s that gets the profile inactive & back active just to get the task/actions re-triggered
4. There are tasks that are specific to doing one thing which could've been just one action but no thanks to the android drama going on, I've had to set some checks for SDK level < 29, root & or shizuku being there which if the conditions are satisfied would be one action for things that require root/shizuku else it would either be an entire user input simulation through autoinput or no action at all with just a tts/toast msg saying "x is left enabled/disabled" depending on the display's state (lock set/unset)
5. When importing Automate flows, the flow uri's may differ so you will have to change the uri values in the global var.s (follow setup).
6. profiles to be mutually exclusively enabled/deleted when testing or using are to be done so to avoid clashes, confusion in what to use or any other unexpected behaviours.
7. You may need to recreate profile contexts in case it still gives you warnings of apps not existing even after unselecting everything.
8. Profiles that use the app context may not be completely reliable for apps open in floating/small windows..android limitation...logcat entry didn't work on my non-rooted phone & the WIN var also isn't too reliable...so gotta renter the app or "re-exit" the app that was previously open to get such profiles active
9. Install & configure a TTS engine LIKE google's `speech recognition & synthesis` if none installed & configured already. test the `say` action if you need any voice. The say actions have the "continue task after error" option enabled so the tasks don't fail due to a voice msg action failure, only disadvantage is that the user would not know if the tasks with the say actions actually ran or rightfully ran. A notification will be posted saying what to do should the `say` action fail.
10. <b>you will see inconsistent namings (I couln't come up with anything better than what you see) & tasks & profile names being the same are also intended just so I could make out the difference in certain tasks & profiles...atleast during the time I was building this</b>

# setup
#### a. import the automate flows (the `.flo` files from the `Automate flows` directory) into automate, the tasker project `Lazy_User.prj.xml` into Tasker & the easer zip into easer
#### b. <b>list of profiles to be mutually exclusively enabled/deleted</b>
1. `Mobile Data Var Set via Automate` & `Mobile Data Var Set`
2. `(Un)Set Sim Presence Airplane Mode via Automate` & `(Un)Set Sim Presence Airplane Mode`
#### c. <b>Tasker Configuration</b>
1. copy `app_ctx_invisible_apps_for_fg_unset_profile.txt` into `/sdcard/Tasker/projects/` or wherever you like but the path where you copy this file must be the set in the `Read File` action's (action 1) `File` arg of the anonymous task belonging to the `App Context Invisible Fg Apps` profile.
2. copy all the automate flow (flow/<id>) & flow beginning id's (flow/<_id>/statments/<id>) from all the imported flows beginning & paste it into tasker's global vars that begin with `AUTOMATE` in `Lazy Workflow Initializer` task's actions 26-33 which has the labels `globals for automate flow id's` (don't bother with `AUTOMATE_CONTENT_BASE_URI`, action 25, since that's the variable to store automate's content provider's authority).
3. run `TTS Test` task to check if the `say` action works which if it doesn't you will get a tasker notification with a button labeled `possible fix` (also refering to Disclaimer 9)
4. Toggle data & bluetooth from tasker's `run an action` option (3 dots on top right location of the main activity -> More -> Run an action) & select the "never ask again button" from the popup you get for TaskerSettings helper app to work without autoninput's "simulation" at work (possibly....didn't work reliably on my non-rooted android, only changed the UI state in the quick settings tile & mobile data settings. no actual system/settings level changes), which if it doesn't, the autoinput simulation will be put to work (on non-root android), else the bluetooth & wifi "toggle" action alone will do the work if root/shizuku is available/running.
5. set your apps you always need WAN connectivity for when in foreground/opened in the `Fg Net Flags Set` profile's app context or recreate it (ANY & EVERY app that you ABSOLUTELY DEEM needing net including email clients, net scanners, maps, games, browsers, ftp clients etc. all goes here) & the same is applicable for `Fg Net Flags Set (Optional)` profile except it's for apps that you deem as MAY NEED WAN connectivity which is decided through a prompt
6. set your email client in the `Email Auto Sync (Un)Set` profile's App context (Also needs to be set in the `Fg Net Flags Set` or `Fg Net Flags Set (Optional)` and or in the "Apps" section of autonotification event context of the `Bg Net (Un)Set` profile)
7. `Fg Net Flags UnSet` - recreate the app context with the invert option selected & set `mobile data settings` app or the `settings` app itself & any other apps you DON'T want this profile's app context to be picked up on while being in that app/activity. Idea is something along the lines of "need wan connectivity for f-droid to download stuff (which is already downloading) & all of a sudden get a call & the call activity takes the foreground over f-droid's activity & you don't want that wan connectivity to be disturbed while the call activity is in the foreground & until the download is complete. I have set only play store & whatsapp on both activities & added some settings activities that the app profile trigger showed in the `Fg Net Flags UnSet` profile to make it more easier to set up
8. set your apps (for notification) in the `Bg Net (Un)Set` profile's auto notification event...idea is to set WAN connectivity enabled for those apps you would want the network to be left enabled until done, like downloads
9. set the map apps you use in the `Map Bg Flags (Un)Set` profile's app context
10. Open up `Toggle Data` task & replace the actions in the Autoinput Action (the configuration argument) at action number 15 with whatever actions you need to perform to get cellular data enabled from the `Mobile Data settings` activity. (the activity, button placement, UI differs from android flavour to flavour)
11. `Gaming Clock`, `Regular Cpu Clock` & `Low Clock Cpu`. Reconfigure the cpu list with their respective low & high clocks & governors. (I have set my `Gaming Clock` task to the highest clock that was available with all core governors to performance & the numbers you're seeing are ones on my unrooted phone) & the `Regular CPU Usage` all to schedutil & `Low Clock Cpu` all to powersave & all cores to whatever I could get the lowest number at which is in Khz & something I pulled from my unrooted if you're wondering why that many zeros in the clock numbers (whatever governor values you're seeing are the only governors available for both my phone's kernels). you're gonna need to delete/add some cpu actions & change their params depending on your phone's hardware
12. `(Un)Set Sim Presence Airplane Mode` or `(Un)Set Sim Presence Airplane Mode via Automate` (profiles to be mutually exclusively enabled). The only difference between the 2 profiles is that the Action in the `intent received` event from both profiles are
    - android's built-in `SIM_STATE_CHANGED` (used by `(Un)Set Sim Presence Airplane Mode`) broadcast is said to be unreliable since android 13 (https://issuetracker.google.com/issues/302614301). I haven't faced that issue on android 13. Only "weird" thing is that I've been seeing that profile being triggered with that broadcast many times even when I haven't ejected/inserted the sim card
    - `SIM_PRESENT` (used by `(Un)Set Sim Presence Airplane Mode via Automate`) is a custom broadcast sent from automate to tasker & the `Tasker - Sim Check.flo` needs to be left running for this profile & it's task to work as expected
13. `Mobile Data Var Set via Automate` or `Mobile Data Var Set` (to be mutually exclusively enabled or deleted by the user)
    - `Mobile Data Var Set` - do a `find setting` (the magnifying glass next to the `Name` field) in the profile's `custom setting` context, toggle cellular data & you got the custom setting name that gets you the cellular data state filled in the Name field
    - `Mobile Data Var Set via Automate` will need the `Cellular Data state` flow beginning from `Tasker - settings & states checks` to be left running
14. At `Ping Test` task you might want to make changes in the numbers at action 14 & 18 (conditional actually) if you think the ping numbers don't apply to your use case
15. you may need to change the `Data` field in the `Send Intent` action in all tasker tasks which are intended to invoke Automate flows (the flow to be invoked will be indicated in the `send intent` action's label field) to whatever the content has been changed to in the flow's start block. do this IF the "Flow URI" field in the imported automate flow's start block differs from what's there in tasker's `send intent` action's Data field.
16. w.r.t Disclaimer 5, replace the values in the var.s in the actions 26-33 in the task `Lazy Workflow Initializer` with content uri's of the flow beginning blocks of the imported flows. The base content uri is set in `AUTOMATE_CONTENT_BASE_URI`. you just need to set the flow id & flow beginning id's from the content uri's of the flow beginning blocks in the aforementioned actions. (fyi that the flow names that the intents will be sent to is set in the action's label)
17. reboot the phone or run the `Lazy workflow initializer` TASK (after disabling actions 10 & 22 or chaning the condition in action 10. revert your changes after the initialization's done) to initialize global vars & run the needed automate flo's based on the selected profiles
18. `Get Recents` profile - doesn't make too much of a difference if this doesn't work, this is just an additional "timeout" addition for when you go to the recents screen. Try this out without doing anything to it which if it doesn't work then proceed with the setup for which you would need to know the text embeded in your navigation bar's recent's button & the button's element id to be set in the event context & in the tasks's action's if statment, which MAY vary with android flavour but for me this wasn't the case when I tried it on an oem rom & a custom rom with varying android versions (text - Overview, elementid - com.android.systemui:id/recent_apps). if there is a variation or you find that this profile is never getting active try below
    - disable this profile, open the `Autoinput UI update` event remove the value set in the Element Text field
    - enable action 2 and or action 3 (either or both works) in the task 
    - enable the profile then tap the recents button.
    - disable the (if you're not okay with constant popup annoyance on every tap) profile & then put in the "Text" in the `autoinput UI update` event context's Text field & the elementid in the condition of action 1 from the task

[optional things to setup](#optional.md)
[working](#working.md)

