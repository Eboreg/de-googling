# My de-googling experience

Like so many of us, I was sick and tired of being constantly tracked and monitored by Big Tech. But it was very much [Rob Braxman's videos](https://odysee.com/@RobBraxmanTech:6) that got me started on actually doing something about it, so big thanks to him for that.

Midway through, I decided to document the project in this manner, both for myself and for anyone who might find it useful in any way. Some of it is very specific to my particular phone model, but much isn't.

In 2024, I made a major overhaul by switching from [E](https://e.foundation/) to [LineageOS for MicroG](https://lineage.microg.org/), and decided to scrap a bunch of no-longer-relevant stuff from this document. There is an archived version [here](README_old.md).

This is an ever evolving document.


# Before

I use a Samsung Galaxy S9 (codename `starlte`). My most used Google apps on it were probably Keep, Youtube[*], and Drive. I host my own email since many years back, but I used the Gmail app to access it. I used Google Calendar, albeit with another app as frontend. And yes, I also synced my phone's photos with Google Photos. :-/

When backing up my computers, I also used Drive for storage; however, the files I sent were already encrypted by [Duplicati](https://www.duplicati.com/), so at least I had _some_ sense. And of course I use Firefox for browsing, and [Duckduckgo](https://duckduckgo.com/?q=rick+astley+never+gonna+give+you+up&iax=videos&ia=videos) for searching (only downsides: ~~Google's image search is admittedly better~~[**], I can't seem to get rid of all those search results in Chinese, and the name "Duckduckgo").

[*] No, I will not spell it "YouTube". That is not how names are written.

[**] Edit: Not anymore; it seems they have fucked it up with some "Google Lens" nonsense.

# Choosing a distribution

For a couple of years, I used the impossibly named LineageOS fork [/e/](https://e.foundation/) (which I will refer to as "E", because that _is_ how names are written), mainly because I wanted to use [MicroG](https://microg.org/) in order to receive push notifications and such stuff, which is not included in [vanilla LineageOS[*]](https://lineageos.org/), and the [LineageOS for MicroG](https://lineage.microg.org/) project didn't have any builds for my phone at that time.

However, in 2024 I discovered that there _was_ now a _LineageOS for MicroG_ build for my phone. And since I had gotten my entire motherboard changed after dropping my phone into the Arctic ocean (long story), I thought I may as well try it out. And it's been a joy to use! I can't really point out any specific reasons, but it's just generally less of a hassle than with E.

[*] I make an exception for names that contain acronyms.


# Installing the OS

Don't have much to add to [the official guide](https://wiki.lineageos.org/devices/starlte/), IIRC. At least I don't remember there being any significant hitches. Still, I will list the basic procedure:

1. Install [ADB](https://developer.android.com/studio/command-line/adb) and [Heimdall](https://github.com/Benjamin-Dobell/Heimdall)
2. Enable developer options and USB debugging on the phone
3. Download the latest .zip and .img files from [here](https://download.lineageos.org/devices/starlte/builds)
4. Boot to download mode (hold down `Volume down` + `Bixby` + `Power`)
5. Run `heimdall flash --RECOVERY recovery.img --no-reboot`
6. Power off device (`Volume down` + `Power`)
7. Immediately when the screen turns black: boot to recovery (`Volume up` + `Bixby` + `Power`)
8. Select `Factory reset`, then `Format data / factory reset`
9. Select `Apply Update`, then `Apply from ADB`
10. Run `adb -d sideload lineage-[...].zip`
11. Reboot! \o/


# Rooting

For rooting with Magisk, I basically followed the instructions [on this page](https://topjohnwu.github.io/Magisk/install.html), except I didn't need to do the stuff under _Samsung devices_ (probably because a custom ROM was already installed). Which is just as well, because I couldn't get any of the stock firmware download tools to work.


# Updating the OS

Nothing noteworthy here. But Magisk will probably need to be reinstalled afterwards.


# Replacing Google

## The simple stuff

* Gmail -> [K-9 Mail](https://k9mail.app/) (simple because I really don't use my Gmail address for other than junk, and don't need to sync it on my phone; there probably is a way to do this, but I haven't bothered looking into it)
* Youtube -> [Newpipe](https://newpipe.net/) (with migration of my subscriptions etc via Google Takeout)
* Maps -> [Osmand](https://osmand.net/) (I use the the map source "OsmAnd (online tiles)", which I think looks nicest. Unfortunately, app settings seem to reset after [OS update](#updating-the-os), so make sure to back them all up as soon as you're satisfied with them)
* Google Authenticator -> [AndOTP](https://f-droid.org/en/packages/org.shadowice.flocke.andotp/)

## Play Store

First choice for us [RMS](https://stallman.org/) wannabes is of course [F-Droid](https://f-droid.org/).

For non-FOSS apps, I just use the preinstalled *App Lounge* application, where it seems you can access all of Play Store's apps anonymously, including my local Swedish services such as Swish and BankID (which work flawlessly, BTW). There are some annoying graphical bugs still, but basically it works fine.

## Calendar

I started out by installing [a basic CalDAV server](https://radicale.org/v3.html) on my trusty Raspberry Pi. But then I realised I wanted to try out that [Nextcloud](https://nextcloud.com/) thing everybody is raving about, so I installed that too on my poor Pi (with much help from [this page](https://docs.nextcloud.com/server/latest/admin_manual/installation/nginx.html), as I was already running Nginx). Not surprisingly, it runs a bit sluggish, and large imports tend to require some retries and also increasing the server timeout limits. But for my humble needs, it will probably suffice. Importing the `.ics` file exported from Google Calendar was a breeze IIRC. On the phone, I just use [the preinstalled Etar](https://f-droid.org/packages/ws.xsoh.etar/). 

## Keep

I love lists; nay, I _need_ lists. For all kinds of lists and short notes, I have been using Google Keep. I tried out a bunch of open source alternatives, but the one I landed on is [Carnet](https://www.getcarnet.app/). It looks like a more beautiful Keep and has all the features I want. Also, I can sync it with the Nextcloud I already installed, as well as import my old Keep items (via Google Takeout again) using [their Nextcloud web app](https://apps.nextcloud.com/apps/carnet). The only major downside is that after clearing and restoring the _Data_ partition, which I do when [updating the OS](#updating-the-os), it tends to throw all my notes in the trash and behaving as a new install, forcing me to manually recover my old notes! But this is a nuisance at most.

### Update 2023-08-06

I wasn't really satisfied with Carnet, so I set about creating my own note/checklist app, called Retain. It is open source, can sync its data via Nextcloud, SFTP, or Dropbox, and is available [here](https://github.com/Eboreg/Retain). Please note that it's in beta. Already works good enough for me, though!

## Drive & Photos

The downside of Nextcloud is of course that you have to host your files yourself, or rely on there existing a Nextcloud integration with your cloud storage provider of choice (and the supply of such integrations honestly leaves a lot to be desired). So, as I neither want to invest in a NAS (plus the fact that backups should really be hosted off-site), nor want to settle for Dropbox or Onedrive, I decided to outsource the "file storage" part of my cloud solution to [Mega](https://mega.io/), who offer end-to-end encryption as well as native Linux (including Raspbian!) clients. 

### Nerd notes

The only major downside is that there isn't a really efficient and reliable way to mount my Mega files as a local directory. The [Mega CMD](https://mega.nz/cmd) utils include tools to serve your files via WebDAV and FTP, which in turn enables you to mount using [davfs2](https://savannah.nongnu.org/p/davfs2) or [CurlFtpFS](http://curlftpfs.sourceforge.net/), respectively. But the WebDAV solution has been a bit janky for me, sometimes refusing to work because of filenames with some weird characters, and the FTP alternative is in beta and has also been a bit buggy for me.

The Mega SDK source code includes an [example implementation of a FUSE module](https://github.com/meganz/sdk/tree/master/examples/linux), but it's extremely basic and does not implement any caching. This makes transfers way too slow for usages such as video streaming. I have actually been working on my own FUSE module, but as my C++ skills leave a lot to be desired, the future for this project is uncertain.

## Software keyboard

This is probably where it makes the most sense to use open source software, since this app literally sees everything you type. Here is also where I ended up sinning, as Swiftkey is simply too damn good for me to switch, and none of the FOSS keyboards were to my satisfaction. But I did try!

[Openboard](https://f-droid.org/en/packages/org.dslul.openboard.inputmethod.latin/) looks to me like the best alternative, although [Florisboard](https://f-droid.org/en/packages/dev.patrickgold.florisboard/) shows great promise.

The Openboard versions in the app stores didn't have a Swedish wordlist, though (although this may have changed by now). To get that, you either need to build the app from [source](https://github.com/dslul/openboard) or trust one of the [user built packages](https://github.com/dslul/openboard/issues/454) (which you probably shouldn't).

## Chromecast

I regularily used my Chromecast device for listening to music and watching video. This is obviously out of the question now, as casting to Chromecast requires proprietary Google services (with the exception of VLC, which somehow manages to do it anyway?!). I replaced it with a Raspberry Pi, on which I installed [OSMC](https://osmc.tv/), which allows me to painlessly stream Netflix, Youtube, and local videos with [Kodi](https://kodi.tv/), while at the same time offering the freedom and familiarity of a Debian installation. I control it from my phone using [Kore](https://f-droid.org/en/packages/org.xbmc.kore/).

### Spotify

In order to run Spotify on this Raspberry Pi, use [Raspotify](https://dtcooper.github.io/raspotify/), which is a thin wrapper over [Librespot](https://github.com/librespot-org/librespot):

```shell
sudo apt install curl apt-transport-https git python
curl -sL https://dtcooper.github.io/raspotify/install.sh | sh
```

Now edit `/etc/raspotify/conf` with your account credentials and other settings to your liking. Then, to make librespot run as a daemon on startup, copy this to `/etc/init/raspotify.conf`:

```
description "Daemonized librespot"

start on net-device-up
stop on runlevel [!2345]

respawn

script
	set -a
	. /etc/raspotify/conf
	exec /usr/bin/librespot
end script
```

Your Raspberry Pi should now pop up as a device in your Spotify clients. Works like a charm for me (except for [one issue](#raspotify-not-working) that showed up in summer 2022):

![image](https://user-images.githubusercontent.com/1786886/156905263-df935a4c-4f17-4439-a27c-d2dcaca02fcc.png)


# The Bixby button

My phone is one of those equipped with an extra button, originally hardcoded to invoke Samsung's stupid Bixby assistant. I used to reassign it however I wanted (specifically, short press: play/pause media, long press: "do not disturb" on/off, double press: flashlight on/off) with the brilliant [BxActions](https://apkpure.com/bixbi-button-remapper-bxactions/com.jamworks.bxactions), but apparently, this requires the Bixby software to be installed, which it's not now (nor would I want it to be). I ended up manually re-mapping the button to toggle playing/pausing media, which is what I mostly wanted it to do. Here is a little shell script I wrote to accomplish this:

```shell
#!/bin/sh

# N.B: DON'T USE THIS VERBATIM ON YOUR DEVICE! ONLY FOR INSPIRATION.

adb -d root
adb -d remount
adb -d shell "cd /system/usr/keylayout && cp gpio_keys.kl gpio_keys.kl.backup"
adb -d pull /system/usr/keylayout/gpio_keys.kl ./
grep -q '^key 703' gpio_keys.kl
if [ $? -eq 0 ]; then
    # key found
    sed -i 's/^key 703.*$/key 703 MEDIA_PLAY_PAUSE/' gpio_keys.kl
else
    echo 'key 703 MEDIA_PLAY_PAUSE' >>gpio_keys.kl 
fi
adb -d push gpio_keys.kl /system/usr/keylayout/
# Should not be needed, but just for safety:
adb -d shell "cd /system/usr/keylayout && chown root:root gpio_keys.kl && chmod 644 gpio_keys.kl"
# Now reboot the phone.
```

Basically, it's just re-mapping key 703 (which is the Bixby button) to trigger the `MEDIA_PLAY_PAUSE` action. Nothing complicated.


# Non-phone de-googling

For those instances where you need to use Google services on your computer, I can recommend the add-ons [Firefox Multi-Account Containers](https://addons.mozilla.org/firefox/addon/multi-account-containers/) and [Google Container](https://addons.mozilla.org/firefox/addon/google-container/), which will at least keep your Google identity from leaking all over the place.

If Google requires two-factor authentication, you can select the option "Get a verification code from the **Google Authenticator** app", but use an open source TOTP app instead, like the excellent [AndOTP](https://f-droid.org/en/packages/org.shadowice.flocke.andotp/).


# Various tips & fixes

## Starting a root shell

This should normally be pretty straight forward:

```shell
adb -d kill-server
adb -d root
adb -d shell
```

(I add the `-d` to make it work on the USB connected device, as I also have an emulated phone created in Android Studio.)

However, this normally results in this happening for me:

```shell
klaatu@jacob:~/e$ adb -d root
* daemon not running; starting now at tcp:5037
* daemon started successfully
restarting adbd as root
timeout expired while waiting for device
```

Solution: Go to _Settings_ -> _System_ -> _Developer options_ on the phone, disable _Android debugging_ and immediately re-enable it. `adb -d shell` is now working.

## Raspotify not working

In July 2022, my [Raspotify installation](#chromecast) suddenly refused to play anything; librespot panicked and complained about "channel closed". I found [this issue](https://github.com/librespot-org/librespot/issues/972), which suggested there is some problem with one of Spotify's access points. So I added this to `/etc/hosts` on the Pi:

```
104.199.65.124  ap-gew4.spotify.com
```

... then restarted the Raspotify daemon, and it works again.
