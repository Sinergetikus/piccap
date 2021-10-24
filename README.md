# PicCap - Hyperion Sender App

### What is this? 
The main idea of this is, to have a simple frontend app with autostart feature for the hyperion-webos executable firstly developed by mariotaku here: https://github.com/webosbrew/hyperion-webos and also ported to newer TVs here: https://github.com/TBSniller/hyperion-webos
This app is still in very early untested development.

I think a picture describes what's working and not the best:
![image](https://user-images.githubusercontent.com/51515147/138606355-29f7d43e-2e3c-495e-9f73-fe3b2e15ded5.png)
### What do you need?
At this time root is neccessary. You will also have the Homebrew Channel installed: https://github.com/webosbrew/webos-homebrew-channel
You should also know what you are doing and should have basic knowledge about all this root stuff, because you can brick your TV and I wouldn't take any responsibilty if you did so. Mainly this isn't the case, but you have been warned. 

### Before first appstart
You have to edit the permission file for the Homebrew Channel, to make it's service available to others:

Connect to your TV with SSH.
Open /var/luna-service2-dev/api-permissions.d/org.webosbrew.hbchannel.service.api.json with 

> vi /var/luna-service2-dev/api-permissions.d/org.webosbrew.hbchannel.service.api.json

Press 'a' to get into editmode and change **org.webosbrew.hbchannel.service.group** to **public**, so you have the following text in this file: 

> { "public": [ "org.webosbrew.hbchannel.service/\*" ], "ares.webos.cli": [ "org.webosbrew.hbchannel.service/\*" ] }

Press 'ESC' to exit editmode, save with typing **:wq!** and pressing 'Enter'. Simply reboot your TV after that.
___
You can also run this command:
`sed -i 's/org.webosbrew.hbchannel.service.group/public/g' /var/luna-service2-dev/api-permissions.d/org.webosbrew.hbchannel.service.api.json; reboot`
And check after reboot:
`cat /var/luna-service2-dev/api-permissions.d/org.webosbrew.hbchannel.service.api.json`

### After first start
Wait a few secounds to let the service elevate root permissions through the Homebrew Channel-Service. After that do a full reboot (no powercycle) of your TV using SSH and typing the reboot command, or removing the power source for a few secounds. The app will tell you when it's ready to get rebooted.

### Old TV, new TV?
Actually we reversed four different librarys which are used by LG TVs to capture the screen. That's why this app covers two different ways of handling these librarys.

For older TVs (~2020 and before) it is libvt for capturing video and libgm for capturing the UI.

On newer TVs (~2020 and after) it is libvtcapture for capturing video and libhalgal for capturing the UI.
For using libvtcapture LG started to do some permission handling, so we need to do this also for our custom executeable. Just see `piccap.service/setlibvtcaptureperms.sh` to get an idea. 

You can also find some information about these librarys here: https://github.com/webosbrew/tv-native-apis

### How to use
Simply fill in your parameters and press save. The configuration files will then be saved to `/var/luna/preferences/` and reloaded to the application.
Simply press start after the application ended loading (~30 secounds after start). 

The start delay timer is needed on newer TVs, because the executeable acutally doesn't check for a running video. Hyperion-WebOS for newer TVs can only run, if there is a video playing. So the steps would be: Set it to 30 secounds, click start and go to an app which is playing a video (eg. YouTube/Twitch or TV). If you don't play a video in this 30 secounds, Hyperion-WebOS will fail to start.

### How to install
Download the ipk from releases or build it all yourself.

Then using ares:
`cmd.exe /c E:\webOS_TV_SDK\CLI\bin\ares-install.cmd -d YOURTV E:\Downloads\org.webosbrew.piccap_0.0.7_all.ipk`

Or manually:
Copy to TV:
`scp /home/USER/downloads/org.webosbrew.piccap_0.0.7_all.ipk root@TVIP:/tmp/org.webosbrew.piccap_0.0.7_all.ipk`
And installing on TV using: 
` luna-send -i -f luna://com.webos.appInstallService/dev/install '{"id":"org.webosbrew.p
iccap","ipkUrl":"/tmp/org.webosbrew.piccap_0.0.7_all.ipk","subscribe":true}'`



### How to build
Put both compiled versions of hyperion-webos versions in service folder.
libvt (https://github.com/webosbrew/hyperion-webos): `piccap.service/hyperion-webos_libvt`
libvtcapture (https://github.com/TBSniller/hyperion-webos): `piccap.service/hyperion-webos_libvtcapture`
`mkdir build`
`ares-package ./piccap ./piccap.service -o build`


# Credits
As stated in the application:
Love to Mariotaku https://github.com/mariotaku
Love to JohnPaul https://github.com/Informatic
Love to OpenLG-Discord https://discord.gg/9sqAgHVRhP
All this wouldn't be possible without them!