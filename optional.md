### optional things to do/setup (remove/disable the profile..whatever you see fit)
1. set your games in the `Gaming` profile's app context
2. remove/disable the `Work Profile Set` profile & if needed/wanted remove the WORK_PROFILE global var from all other actions, tasks, conditions & profiles (this is necessary IF the `Work Profile Set` profile's removed). The action for it is created the way it is for when corporate apps are installed onto a work profile & one needs to show their activity as online at all times or needs to be responsive & needs connectivity at all times as long as there's some source of connectivity & for as long as the work profile's active.
3. Enable the actions in the 2nd task of the `screen Off Low Power` & `screen Unlocked No Low Power` profiles
4. if you plan to use the profiles under the `Battery Audios` separator profile you would need your custom audio files as well. else delete/disable those profiles
5. (disable/delete if not needed which you would need to do to the `OBD2 (Un)Set` profile as well) set your obd apps as the entry app in `OBD2 Fg Flags (Un)Set` profile & the very same obd apps as auto notification entry in `OBD2 Bg Flags (Un)Set` profile
6. set an audio file in the `Music Play` action in `Unplugged 1 Pcent Batter`, `Unplugged 2 Pcent Battery`, `Battery Ful Audio Event`, `Power Connected` entry tasks & a list of audio of files (can be one file too since this is just selecting random files from said list) in to %par1 field of the `perform task` action that perform's `Random Audio Play` task in entry task of profiles `Phone Shutdown Audio`& Exit task of `Battery Audio Toggle` profiles
7. To be done in `Tasker - Battery Noises` flow (which is `(Template) Tasker - Battery noise.flo` in this repo) - set your preferred audio file paths in all the arrays across all the flow beginnings (even add more to the arrays if wanted/needed) & set your audio files as values into the array (battery_range) belonging to the `Battery State` flow beginning
8. Enable `Battery Audio Toggle` if Battery Audio tasks/profiles are to be used
9. if you do not want the toast/tts msgs from the `Ping test` & `WAN check` the actions to disable/delete are the following
    - `Ping Test` Task
        - action 5
        - action 10
        - action 16
        - action 19
        - action 22

    - `WAN Check Switch` Task
        - action 3
        - action 19
        - action 24
        - action 28
        - action 38
        - action 52
        - action 60

