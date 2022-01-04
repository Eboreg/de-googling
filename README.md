# My de-googling experience

Like so many of us, I was sick and tired of being constantly tracked and monitored by Big Tech. But it was very much [Rob Braxman's videos](https://odysee.com/@RobBraxmanTech:6) that got me started on actually doing something about it, so big thanks to him for that.

Midway through, I decided to document the project in this manner, both for myself and for anyone who might find it useful in any way.

## Before

I use a Samsung Galaxy S9 (codename `starlte`). My most used Google apps on it were probably Keep, Youtube, and Drive. I host my own email since many years back, but I used the Gmail app to access it. I used Google Calendar, albeit with another app as frontend. And yes, I also synced my phone's photos with Google Photos. :-/

When backing up my computers, I also used Drive for storage; however, the files I sent were already encrypted by [Duplicati](https://www.duplicati.com/), so at least I had _some_ sense. And of course I use Firefox for browsing, and [Duckduckgo](https://duckduckgo.com/?q=rick+astley+never+gonna+give+you+up&iax=videos&ia=videos) for searching (only downsides: Google's image search is admittedly better, and I can't seem to get rid of all those search results in Chinese).

## Choosing a distribution

My first choice was [LineageOS](https://lineageos.org/). However, I soon learned I would need [MicroG](https://microg.org/) in order to receive push notifications and such stuff ... I think. My understanding of this is a bit patchy. Anyway, vanilla LineageOS does not include MicroG, the [LineageOS for MicroG](https://lineage.microg.org/) project don't have any builds for my phone (also, their site was down at the time), and I didn't want to make the installation more of a hassle than necessary (I don't really have a backup phone should I brick my current one). Solution: I went for the impossibly named LineageOS fork [/e/](https://e.foundation/).

## Installing the OS

I basically followed the instructions [here](https://doc.e.foundation/devices/starlte/install) (with some assistance from [here](https://www.getdroidtips.com/lineage-os-18-1-samsung-galaxy-s9/)), with one exception; instead of the `no-verity-encrypt` variant linked, I ended up using the one described [here](https://forum.xda-developers.com/t/deprecated-universal-dm-verity-forceencrypt-disk-quota-disabler-11-2-2020.3817389/). I don't exactly remember why, but I guess the first one didn't work for some reason. Perhaps I was doing it wrong.

Going forward after installing [TWRP](https://eu.dl.twrp.me/starlte/) turned out to be a hassle. I couldn't seem to get the phone to shut down or boot into recovery from download mode; holding down `power + volume down` (or if it was `up`) would just reboot it normally, which overwrote TWRP and rendered the whole exercise pointless. After many attempts, it turned out it would actually shut down if I did this with the USB cable still attached!

After this, I could actually boot into TWRP and get the new OS installed. I don't remember any major obstacles during this process; there probably were some, but none that a little stubbornness couldn't fix.

## Replacing Google

### The simple stuff

* Gmail -> [K-9 Mail](https://k9mail.app/) (simple because I really don't use my Gmail address except for junk, and don't need to sync it on my phone; there probably is a way to do this, but I haven't bothered looking into it)
* Youtube -> [Newpipe](https://newpipe.net/) (with migration of my subscriptions etc via Google Takeout)
* Maps -> [Osmand](https://osmand.net/) (I use the the map source "OsmAnd (online tiles)", which I think looks nicest)

### Play Store

First choice for us [RMS](https://stallman.org/) wannabes is of course [F-Droid](https://f-droid.org/).

For non-FOSS apps, I just use the conveniently titled ["Apps"](https://gitlab.e.foundation/e/apps/apps) app that came preinstalled; only for some special cases, like my Swedish bank's application (yes, it works, and so does Swish and BankID!), have I needed to consult places like [APKPure](https://apkpure.com/).

### Calendar

I started out by installing [a basic CalDAV server](https://radicale.org/v3.html) on my trusty Raspberry Pi. But then I realised I wanted to try out that [Nextcloud](https://nextcloud.com/) thing everybody is raving about, so I installed that too on my poor Pi (with much help from [this page](https://docs.nextcloud.com/server/latest/admin_manual/installation/nginx.html), as I was already running Nginx). Not surprisingly, it runs a bit sluggish, and large imports tend to require some retries and also increasing the server timeout limits. But for my humble needs, it will probably suffice. Importing the `.ics` file exported from Google Calendar was a breeze IIRC. On the phone, I just use [/e/'s preinstalled fork of the Etar calendar app](https://gitlab.e.foundation/e/apps/calendar). 

### Keep

I love lists; nay, I _need_ lists. For all kinds of lists and short notes, I have been using Google Keep. I tried out a bunch of open source alternatives, but the one I will probably use is [Carnet](https://www.getcarnet.app/). I haven't tried it out extensively yet, but it looks like a more beautiful Keep and seems to have all the features I want. Also, I can sync it with the Nextcloud I already installed, as well as import my old Keep items (via Google Takeout again) using [their Nextcloud web app](https://apps.nextcloud.com/apps/carnet).

### Drive & Photos

The downside of Nextcloud is of course that you have to host your files yourself, or rely on there existing a Nextcloud integration with your cloud storage provider of choice (and the supply of such integrations honestly leaves a lot to be desired). So, as I neither want to invest in a NAS (plus the fact that backups should really be hosted off-site), nor want to settle for Dropbox or Onedrive, I will probably have to outsource the "file storage" part of my cloud solution to [Mega](https://mega.io/), who offer end-to-end encryption as well as native Linux (including Raspbian!) clients. Perhaps there is some convoluted way of integrating this into Nextcloud, but it's not a big deal. I've had good experiences with Mega in the past and will probably upgrade to a paid subscription.

## Still to be solved

### Chromecast

I regularily use my Chromecast device for listening to music and watching video. This is obviously out of the question now, as casting to Chromecast requires proprietary Google services (with the exception of VLC, which somehow manages to do it anyway?!). One alternative seems to be [Miracast](https://en.wikipedia.org/wiki/Miracast). I'll probably buy a cheap gizmo and try it out.

A perhaps less optimal alternative would be to use my Raspberry Pi for casting, by way of [`omxiv` and Raspicast](https://thepi.io/how-to-use-your-raspberry-pi-as-a-chromecast-alternative/). Less optimal because it seems to severely limit the number of apps I can stream from (not that I know that Miracast doesn't also do this), and because my poor Pi is overworked as it is.

### The Bixby button

My phone is one of those equipped with an extra button, originally assigned to Samsung's pesky Bixby assistant. I used to reassign it however I wanted (specifically, short press: play/pause media, long press: "do not disturb" on/off, double press: flashlight on/off) with the brilliant [BxActions](https://apkpure.com/bixbi-button-remapper-bxactions/com.jamworks.bxactions), but now I can't seem to get it to detect this button, and the shell scripts that should be installed with the app (`start_full_remap.sh` etc) don't exist?! Don't know if I will get this fixed.

## Non-phone de-googling

For those instances where you need to use Google services on your computer, I can recommend the add-ons [Firefox Multi-Account Containers](https://addons.mozilla.org/firefox/addon/multi-account-containers/) and [Google Container](https://addons.mozilla.org/firefox/addon/google-container/), which will at least keep your Google identity from leaking all over the place.

If Google requires two-factor authentication, you can select the option "Get a verification code from the **Google Authenticator** app", but use an open source TOTP app instead, like the excellent [AndOTP](https://f-droid.org/en/packages/org.shadowice.flocke.andotp/).
