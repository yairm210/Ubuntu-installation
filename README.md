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
