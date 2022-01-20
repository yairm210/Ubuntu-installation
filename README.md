# Ubuntu-installation
Documenting the process so next time it's easier

Install, without downloading drivers. Restart.

Screen starts having slight problems during the install, after restart it doesn't even load, all I get is a flashing cursor.
Added 'nomodeset' to grub file, and ran `sudo update-grub` - worked!

Couldn't download steam. 
Tried `sudo apt update`, `sudo apt upgrade`, nothing. 
Downloaded Synaptic, 'fix broken packages', checked for broken packages, none - steam *still* wouldn't download. 
Restarted and suddenly it worked! Mysterious.

Restarted, updated `snapd` and `snap store` from the snap store, sounds like a good first step before actually downloading things. 
Updated Gnome. Snap store hadn't finished its update, so I closed it, thinking that the fact that it was open was the blocker. But I couldn't reopen it.

After restarting, still couldn't. System monitor showed that 'snap-store' was being started, then stopped.
`snap-store` command returned `ERROR: not connected to the gnome-3-38-2004 content interface.` Okay then. `snap refresh snap-store` says it has no updates available.
`snap remove snap-store` gives `error: snap "snap-store" has "refresh-snap" change in progress`. So I guess the lesson is don't try and update the stap store from within the snap store.

`snap changes` gives the list of tasks, `snap abort` cancells the update. It's still not starting - error loading shared libraries, I guess the rollback wan't as rollback-y as it should have been, but that's what you get for interrupting midway.
Before we try to unintsall-reinstall, try `snap refresh snap-store` to redownload the latest version. Seems to work, taking an awful long time to connect eligible plugs and slots. I can't tell when I started running that, I should add a timestamp to all commands.
After it finished updating, it opens up!

`sudo snap install android-studio --classic` for Android Studio

vscode from the snap store
