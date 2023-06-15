# installing openwrt on a tp-link google onhub

was tracking this thread as it developed: <https://forum.openwrt.org/t/onhub-tp-link-tgr1900-future-support/17899/385?page=2>

anyway it happened, and it's at a point where it's basically usable and ~stable, will be officially supported in 23.05 i think

## flashing

wiki page: <https://openwrt.org/inbox/toh/google/onhub_tp-link_tgr1900>

### steps i took

prepare the install usb:

- download the firmware and rename it openwrt.bin, and plug in a spare usb drive which showed up as /dev/sdg
- `sudo dd if=openwrt.bin of=/dev/sdg`

prepare the hardware:

- peel off the soft circular pad on the base of the router, unscrew the hex nut (1.5) and there will be a "developer mode" button somewhere below it (you can't see it but trust me it's there sorta). you'll need this in a bit

boot sequence:

- plug in the usb
- hold the reset button on the back (next to the ethernet and power) as you plug the power in. wait until the led ring blinks red (not orange), then you can let go.
- press the developer mode button you uncovered from behind the hex nut. fumble around a bit til you hear/feel it press down. the leds should blink purple for a bit and the speaker should beep twice, then the leds go blank as the device boots. press the developer mode button again after the beeps. you'll know it's done when the led ring lights up with multiple colors (rgb).

flash:

- connect ethernet to the lan port and to some other device like a laptop
- ping 192.168.1.1 to make sure it's online
- copy and write the firmware with scp and ssh

```sh
scp -O openwrt.bin root@192.168.1.1:/tmp/

ssh root@192.168.1.1 -C "dd if=/dev/zero bs=512 seek=7552991 of=/dev/mmcblk0 count=33 && dd if=/tmp/openwrt.bin of=/dev/mmcblk0 bs=1M"
```

take out the usb because we're done, openwrt is installed. unplug power and put it back in. it should boot to the onboard storage (after blinking purple and beeping and then going multicolor rgb). brace yourself, because that was the easy part

## configuring

took me a hot minute to find this: <https://openwrt.org/docs/guide-quick-start/start>

### install web gui (if not already installed)

what you specifically probably want to do is to get internet access and install the web gui. this should already be installed once 23.05 releases but on the snapshot i installed, it wasn't. so: <https://openwrt.org/docs/guide-quick-start/ssh_connect_to_the_internet_and_install_luci>

```sh
ssh root@192.168.1.1

uci set network.lan.ipaddr="192.168.1.1"  # if it's different than default
uci set network.lan.gateway="192.168.0.1"  # whatever you use
uci set network.lan.dns="9.9.9.9"  # or whatever you prefer

uci commit
/etc/init.d/network restart
```

ssh will disconnect. reconnect at the new ip.

now install luci: <https://openwrt.org/docs/guide-user/luci/luci.essentials#basic_installation>

```sh
ssh root@192.168.1.1

opkg update
opkg install luci-ssl  # for https. install `luci` if http is fine
```

### gui time

the basics first: password, ssh limits, yknow.

- system > system > general settings: set a hostname and some metadata and timezone
- system > administration:
  - router password: set a good password
  - ssh access: change the interface from "unspecified" (any) to `lan`. maybe also the port if you want. consider turning off password auth.
  - ssh-keys: add ssh keys that you want to be able to login
  - https access: optionally redirect http to https if you installed that earlier

#### network

network > interfaces:

- i went to edit `lan` > dhcp server > general setup, and i changed the limit to 100 instead of 150. i *think* this maps the dhcp range to 101-200 if i understand correctly.

network > wireless:

- you can add the same ssid and security (wpa3/wpa2 mixed mode, most likely) to radio0 (2.4gz) and radio1 (5ghz). this doesn't actually seem to be enough for smart connection switching ("band steering"?) and i noticed pretty much all devices just stuck with 2.4ghz and never connected to 5ghz.

- you could also say fuckit and use different ssids like "network-2g" and "network-5g". this seems less appealing, i'd rather properly figure out how to use the same ssid and get devices to prefer 5g

- idk what radio2 is supposed to be for on the onhub. on the forum, there was some talk about some linksys router having a "third radio" for monitoring DFS, it wasn't meant to be connected to. but idk if this is the same kinda thing. i thought some routers had tri-band to use one of the bands as wireless backhaul in mesh situations.

- set radio0 and radio1 to auto channel
- set radio1 width to 80mhz

network > dhcp and dns > general settings:

- change local server and local domain from `lan` to `local` (better browser support i think)

network > dhcp and dns > advanced settings:

- DNSSEC: turn it on

network > dhcp and dns > hostnames

- add mappings for stuff like `home.trwnh.com => 192.168.1.2` -- one mapping per hostname you want to resolve locally

network > firewall > port forwards:

- set up your port forwarding for home server apps that need it. stuff like `https = wan:443 => 192.168.1.2`

#### some apps?

this heavily depends on what you wanna do, if you wanna run homeserver stuff or whatnot.

system > software

stuff you might want:

wpad-openssl
: fully featured wireless support. the default package (wpad-mbedtls) is stripped down and doesn't fully support roaming or mesh or other stuff you might want.

dnmasq-full
: DNSSEC support?

cli tools for easier ssh times:

nano
: i don't really care for vi

luci apps (gui stuff basically):

luci-app-upnp
: configure upnp

luci-app-attendedsysupgrade
: update openwrt from the web gui

## problems left unsolved

- same ssid for 2g/5g but preferring 5g, i.e. without getting stuck on 2g.  (again: "band steering"?)
  - it sort of works but idk if it actually works. stuff connects to 2g more often than i'd expect. what i did was set 2g power to 15db, leave it at 20mhz 
