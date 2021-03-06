# Ubuntu-installation
Documenting the process so next time it's easier

Install, without downloading drivers. Restart.

## Initial install of apps

Screen starts having slight problems during the install, after restart it doesn't even load, all I get is a flashing cursor.
Added 'nomodeset' to grub file, and ran `sudo update-grub` - worked!

Couldn't download steam. 
Tried `sudo apt update`, `sudo apt upgrade`, nothing. 
Downloaded Synaptic, 'fix broken packages', checked for broken packages, none - steam *still* wouldn't download. 
Restarted and suddenly it worked! Mysterious.

`sudo snap install android-studio --classic` for Android Studio

vscode from the snap store

Settings - appearance - dark

Installed hebrew from search - languages. That's still not enought to actually *type* in Heberew, for that we need to go to Settings > Input and Language > Input sources and add Hebrew. 
#### Snap basics

Restarted, updated `snapd` and `snap store` from the snap store, sounds like a good first step before actually downloading things. 
Updated Gnome. Snap store hadn't finished its update, so I closed it, thinking that the fact that it was open was the blocker. But I couldn't reopen it.

After restarting, still couldn't. System monitor showed that 'snap-store' was being started, then stopped.
`snap-store` command returned `ERROR: not connected to the gnome-3-38-2004 content interface.` Okay then. `snap refresh snap-store` says it has no updates available.
`snap remove snap-store` gives `error: snap "snap-store" has "refresh-snap" change in progress`. So I guess the lesson is don't try and update the stap store from within the snap store.
Alternatively, don't try and update other snaps when updating snapd. The binary you started out using may need to be replaced.

`snap changes` gives the list of tasks, `snap abort` cancells the update. It's still not starting - error loading shared libraries, I guess the rollback wan't as rollback-y as it should have been, but that's what you get for interrupting midway.
Before we try to unintsall-reinstall, try `snap refresh snap-store` to redownload the latest version. Seems to work, taking an awful long time to connect eligible plugs and slots. I can't tell when I started running that, I should add a timestamp to all commands.
After it finished updating, it opens up!


## Nvidia drivers

#### BLUF

Download recommended drivers from "Additional Drivers". They know what they're on about.

You may be playing around with Grub a lot, so write the revert steps on a piece of paper and have it handy:

- `nano /etc/default/grub`
- GRUB_CMDLINE_LINUX_DEFAULT="stuff"
- If you give up on nvidia halfway you can return to normal by removing the drivers with `sudo apt purge nvidia*`

Final Grub configuration that solved all problems - `GRUB_CMDLINE_LINUX_DEFAULT="splash i915.enable_psr=0"`


## Details

nvidia drivers made the whole screen black. Deleted packages through the console, `sudo apt purge nvidia*` and it returned to the default drivers.

Screen felt too large, so tried adding another resolution using [this guide](https://www.cyberciti.biz/faq/ubuntu-linux-install-nvidia-driver-latest-proprietary-driver/) - I was able to screate and set the new resolution but once I did so the computer encountered an 'unexpected error' and logged me out, after login all programs were closed and the new resolution setting no longer existed.   

Steam scrolling is very slow, not sure if that's a Steam problem, since Chrome seems to scroll fine? Also not sure if that's a driver problem...

Apparently I have a GTX MX450 GPU, so maybe I need a different driver? LAst time I tried the 470, but maybe the 450 is what I need?
NOPE the [official nvidia site](https://www.nvidia.com/Download/index.aspx) says it's 470.

Checked internet to see what I'm missing out on, basically - 3d.

Tried a couple of 3d games and yikes. Hardly moving. So let's try and make it work?

Doing a risky move - I installed the Nvidia drivers like last time, but I ALSO removed the 'nomodeset' grub parameter, since [nvidia recommended it](https://forums.developer.nvidia.com/t/black-screen-after-install-of-nvidia-driver-ubuntu/109312/20), AND the 'quiet' parameter as recommended by [this guy](https://forums.developer.nvidia.com/t/possible-solution-for-black-screen-ubuntu-and-latest-460-driver/179850). Lets see!

OKAY. So, what sheband caused a flashy screen like the initial install, and THAT we solved with a `nomodeset`. So I tried the same thing now, and halleluja, everything seems to work!

Grub line now looks like:

> GRUB_CMDLINE_LINUX_DEFAULT="splash nomodeset"

So just without the quiet.

nvidia-driver-470 is in use.
But the 3d is still just as slow and impossible as ever, so that was never our problem... it's the graphics card :(
Oh well

What seems to be happening is that I'm at 100% CPU usage... so is the GPU just not working???


Somehow??? it got blackscreened again. I did get another piece of info, which is that tty6 was stuck on `show plymouth boot screen`. Same trick as last time, esc - safe mode - root terminal - `sudo apt purge nvidia*`. We need to find a way to actually get the GPU utilized, regardless of the driver.

SO this has been a hell of a ride. I did a bunch of things in the meantime and it's hard to tell what actually helped:

 - installed nvidia driver from Drivers in UI - this doesn't mean it's actuve yet!
 - disabled secure boot from BIOS settings
 - blacklisted the OTHER driver (nouveau) as recommended [here](https://forums.developer.nvidia.com/t/black-screen-after-installing-nvidia-driver-470-in-ubuntu-21-04/191053)
 - sudo prime-select intel, and then sudo prime-select nvidia, also recommended there
 - And then finally, GOT RID of the nomodeset and THEN IT WORKED.

So the only thing I know for SURE is that some drivers (nouveau) NEED the nomodeset and others (nvidia) CANNOT have it set.
This was fun.

That WAS worth it though - the display now supports multiple resolutions, the blue light filter seems to actually be engaged, Steam scrolls faster and most noticably - 3d isn't COMPLETELY BROKEN. And Age of Wonders III was playable!!

### Screen flickering

BUT this caused a wierd flickering of the screen when e.g. scrolling on chrome.

Tried adding grub setting as mentioned [here](https://forums.developer.nvidia.com/t/black-screen-and-screen-flickering-in-ubuntu-20-04-with-470-drivers/190071/6) - did not work

Then I tried forcing redraw of entire screen [as mentioned here](https://askubuntu.com/questions/263996/fixing-the-nvidia-graphics-screen-flicker-issue) - 

I tried adding a `/etc/X11/xorg.conf` file with parameters as mentioned [here](https://askubuntu.com/questions/263996/fixing-the-nvidia-graphics-screen-flicker-issue) but if the file so much as exists then nothing will start! 

Also it appears that occasionally we get random mouse freezes.

SO
in order to undo things that didn't work, I went back to the grub file to remove the `i915.enable_psr=0` setting, but it turns out I misspelled it.
After fixing the typo, update-grub, and restart, IT NOW WORKS!
