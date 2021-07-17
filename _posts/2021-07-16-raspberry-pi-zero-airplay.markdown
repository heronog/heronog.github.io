---
layout: post
title:  "Playing audio with Airplay through HDMI with a Raspberry Pi Zero W (Part 1)"
categories: technical
tags: raspberry-pi linux airplay music
excerpt: Simple setup of an Airplay service with a Raspberry Pi Zero W with decent performance and stabililty.
---

![Garage AirPlay speaker on iPhone](/assets/pi0w-airplay/garage-airplay-screenshot.jpg)

## Some background and motivation

Whenever I work out I like listening to podcasts. It keeps my mind busy while I wait for my next set and it helps me feel extra productive whenever I get a workout and learn something new in the same session.

I also hate wearing headphones when working out, so I tried to play my podcasts through a speaker in the garage where I have my exercise space. I happen to have a couple Raspberry Pi Zero W's around from
"in progress" projects and wanted to do something with, as well as an old(er) TV that has been sparely used and mostly spends it's days in storage and I wanted to get some use out of it.

By putting the two together I thought I could get a nice project and maybe get extra motivation to work out more often without having to buy more stuff.

I can say that this is a very specific need, where I use an iPhone to interact with my music/podcasts, but I would guess you can do the same and install a some other service for android compatibility like DLNA/UPnP or somethhg along those lines.

Finally, I think I will be using some of the steps here for more projects, not just audio but IoT/Home automation and linux in general. I hope it's useful to you.

## Goals

In choosing what to do I had some specific things that I want out of this system:
* Use HDMI audio out, since I'll be using my old TV.
* Use Airplay since it's easier to handle on my phone.
* Use very little energy when not in use, or alternatively boot fast, ideally <10s but < 30s is acceptable.
* Show time, track info. Sometimes I want to know if I should hurry up and try to finish my workout before the episode ends, sometimes I want to leave enough episode minutes for the next day's workout.
* Spend as little as possible, use as many things from my spare parts bin as possible.
* Look nice when in use.
* Don't want to be dealing with a bunch of apps or incompatibilities from using different distros for different builds. I have other network players built with RPis and it would be useful to use same distro/software as much as possible.
* Show my workout of the day program for reference? I know this would not be an OOTB feature but would be nice if it was.

## Alternatives and other considerations

I would say here that if you are not planning on using specifically a Pi Zero W and have a different Raspberry Pi or a different board, please consider some of the following distros, they have been designed from the groud up to play audio and have some amazing features. Unfortunately, the Pi Zero W does not have enough speed to run them while streaming and processing audio at the same time.

### Some of the distros and solutions I looked at before this
* Play on phone's speaker 
  * **Pros:** Available now, no extra work or cost, any smartphone can do this
  * **Cons:** Not enough volume, risk of accident with the phone, distracting.
* Bluetooth speaker
  * **Pros:** Cheap enough, compatible with most setups
  * **Cons:**  Requires money to buy stuff.
  * Don't want to buy more stuff, but if buying I would go for a good one which is not that cheap
* [balenaSound][balena-sound]
  * **Pros:** Great architecture, lets you learn how to manage IoT fleets from the cloud with their free service tier, docker based and open source
  * **Cons:** Takes a long time to boot on the pi zero and could not get it to actually play sounds since it was taking too long to initialize. Does  not support the HiFiBerry AMP100 that I have for the other player.
  * This seems like a great way to set up a multiroom network whith support for other services like Spotify, Roon, MPD, Bluetooth, etc. but I would require a different SBC like at least an RPi3 to be performant enough. The overhead of running containers was just too much for the RPi0W.
* [Volumio][volumio]
  * **Pros:** Easy set up and fully featured, open source with premium services. This is one of my favorite distros for sound and have been playing with it for a while, it's supported by home automation systems and has an OK interface, it's not as pretty as others but it has great community support. Bluetooth, roon, spotify, MPD all supported.
  * **Cons:** Takes over 90s to boot on the Pi0W. Sound was choppy and would disconnect from the network from time to time. UI is OK but not great, may not matter too much but it's something to consider. Stable version is a bit old and missing support from some niche DAC/AMP cards. If you don't have a HiFiBerry AMP100 there is nothing to worry about, I have one for the other player and would rather use the same distro.
  * I may want to revisit this one, I've added a repeater close to where this will be and it may help to fix thes issues. It makes things easier and is in general a pretty great distro. Boot times can be managed by just not turning it off, the Pi0W uses very little power so it may be a non-issue.
* [HiFiBerryOS][hifiberry-os]
  * **Pros:** Supports the AMP100, nice UI, open source and fully featured.
  * **Cons:** Takes ages for it to boot on the RPi0W. You can not pre-configure the Wi-Fi for a fully headless install, you have to use the ad-hoc access point feature. Support for the RPi0W will be removed in a future release due to the bad performance, so it's a no-go for this setup. If you have an armv7 system like the RPi3 you should definitely try it. It's more focused and prettier than Volumio.
* Use a different SBC like a more powerful RPi3
  * **Pros:** I would be be able to use any of the above distros without performance penalties as well as having the option to use Ethernet or even a faster USB WiFi adapter, UI is much more responsive and boot times are good enough.
  * **Cons:** I have other plans for my other RPi3s and I would have to buy one extra.
  * I refuse to believe that a [modern 1GHz SoC with 512MB of RAM](https://www.raspberrypi.org/products/raspberry-pi-zero-w/) is incapable of re-encoding an audio stream over the network and sending it down its I/O as designed. Too easy.
* Roll my own
  * **Pros:** I can do whatever I want and add as many useful features I need. I love doing this kind of thing so that's extra points. Don't need to support any features I don't want.
  * **Cons:** I can do whatever I want and add as many features as I need. I love doing this kind of thing so it may take me way longer than it should.
  * I love doing this, so why not? I also have the opportunity to learn something new and implement features that work for me.

## The MVP

This is easier than I thought, I installed a minimalistic distro into the Pi for quick boot and then run [shairport-sync][shairport-sync], an airplay server with multiroom sync capabilities as a service. It's as simple as that, so let's get started.

### Materials
* Raspberry Pi Zero W
* Micro SD Card. I used a 32GB one but I believe a 4GB one would work fine as long as it's high speed.
* [Mini-HDMI to HDMI cable. I used this one](https://www.amazon.com/gp/product/B012ABRPGI) since I had it already.
* Some old tv with HDMI input and speakers.
* Micro USB cable and charger.
* Optional: Some case for the RPi0W. I used the official case that came with it.
* Another computer with a Micro SD reader.

### OS installation
* [RaspberryPiOS]() installer [see video on how to use](https://www.youtube.com/watch?v=ntaXWS8Lk34)
  * Click Select OS ->  RaspberryPi OS (other) -> Raspberry Pi OS **LITE**
  * If supported by your platform (i.e. not mac AFAICT) Ctrl-Shift-X  should let you configure WiFi and enable SSH
  * If the above tool does not work for you or want to configure manually:
    * Remove the MicroSD card and Insert it again. Your computer should show that a new disk named `boot` is available.
    * Open the `boot` disk and create an empty file named `ssh` . You can use a text editor for this, just create a new file and save it under this name. Be careful not to name it `ssh.txt` **The file name should be exactly `ssh` !!** Rename it if necessary to remove the extension.
    * Create a new file in the `boot` disk named `wpa_supplicant.conf` using a text editor and place the following contents in it. Adjust your country, WiFi network name and password.
      ```
country=us
update_config=1
ctrl_interface=/var/run/wpa_supplicant
network={
 scan_ssid=1
 ssid="Your WiFi name"
 psk="Your WiFi password"
}
      ```
* Remove the SD Card from the computer and install it in the RPi0W.
* Connect the TV through HDMI cable and USB power to the port closest to the edge of the RPi. Turn the TV On of course.
* You should see the system boot up and once it's done you should be able to ping it using  `raspberrypi.local` or it's IP address.

### OS Setup
* SSH into the system `ssh pi@raspberrypi.local` with password `raspberry`.
* Reconfigure the system hostname using `sudo rpi-config` and following the menus. Give it a name, in my case it's `garage`
  * While you are there, change the default password to something more secure.
* Reboot the system with `sudo shutdown -r now`
* Once the system is booted back up you should be able to log back in again with `ssh pi@garage.local` (or whatever name you gave it).
* At this point if everything went smoothly you should have a command prompt like `pi@garage.local $ `. [Here is a more involved tutorial][setup-tutorial] in case there are problems.
* Optional: I prefer passwordless logins, so I usually copy my SSH key over AND add the remote host to my ssh config file.
  * `ssh-copy-id pi@garage.local`
  * Add the following lines to `~/.ssh/config` or create it if not there yet (!!)
    ```
Host garage
   HostName garage.local
   User pi
    ```
* SSH back in `ssh pi@garage.local` or `ssh garage` if you did the config file changes.
* Update the system
  * `sudo apt update`
  * `sudo apt upgrade -y` I usually use the `-y` switch so it will just do the installation instead of asking for confirmation, but it can be skipped.
    
### Airplay service setup
This is the easy part! Once you have your OS installed and have more proper SSH access, the only thing missing is to install and enable the Airplay service.

* `sudo apt install shairport-sync -y`

Once this is done, you should be able to see `Garage` in your Airplay speakers list on your Apple devices. If not, reboot the RPi0W with `sudo shutdown -r now` and try again.

## Conclusion

That was easier than expected! but it's not really complete, we just got to a functional MVP that can be used while we add some of the nicer features, but that will have to wait for the next post.
![Garage Splash screen](/assets/pi0w-airplay/garage-splash.png)


[balena-sound]: https://sound.balenalabs.io
[volumio]: https://volumio.org
[hifiberry-os]: https://www.hifiberry.com/hifiberryos/
[shairport-sync]: https://github.com/mikebrady/shairport-sync
[setup-tutorial]: https://www.tomshardware.com/reviews/raspberry-pi-headless-setup-how-to,6028.html