# Build Notes

## ASUS Z170I Gaming
a mini-itx z170 board combo'd with an i5-6600k.

### what to do with this board
- should i use it as a windows gaming machine?
- should i use it as a home server?

right now i installed windows on a cheap nvme drive and have it powered by the evga 500bq; i bought a bitfenix prodigy for it so i guess it can be spec'd out for either use case thanks to that case's spaciousness. (whereas if i had gotten a ds380b it would've had some cooling issues, would've required me to buy an sfx psu, and wouldn't make for a good desktop at all -- so an expensive waste of money)

## ASUS MAXIMUS VIII HERO
z170 atx motherboard that i bought as part of a combo with an i7-6700k.

### observations
- one POST issue has been narrowed down to a weird interaction between thunderbolt support and igpu? specifically it seems to freeze (q code 60, as of latest testing) when both "igpu multi monitor" and "thunderbolt boot support" are enabled... and maybe some other thunderbolt options like usb or just thunderbolt in general, but i can't remember and i don't wanna retest, so i just disabled thunderbolt entirely.
- another POST issue (q code 22 / 55  / occasionally cc?) was happening when a certain stick of g.skill trident z rgb ddr4-3200 was inserted. i sent that kit off for rma, but i've read that perhaps asus motherboards can silently corrupt some earlier gskill trident z revisions. not sure about the validity of that statement but i guess i might wanna take it as an opportunity to sell them off and upgrade to 4x16gb idk

## Supermicro X9DAE

boy do i kinda hate this motherboard after all the problems it's given me and how meh supermicro's rma process has been

### gotchas

#### uefi boot usb must use usb2.
booting from uefi usb seemingly doesn't work, until you put the usb key into a **usb2** port.
the two rear usb3 ports **do not work** for this!

#### lpddr3 is gaslighting me.
16x8gb samsung pc3L-12800R (lpddr3-1600 equivalent) would not work reliably...
...until it suddenly does, and then again suddenly doesn't.
i thought i had a dead stick because it wouldn't boot with all 16 filled.
then i thought it was misaligned cpu2 pins
then i thought it was cpu2 power delivery
then shuffling the sticks around made it somehow work... temporarily
clearing cmos made ALL the samsung ecc ram stop working
...but some standard corsair / kingston ddr3 ram works perfectly fine!
i still don't know if the ecc ram i bought is working,
or if the motherboard is incompatible, or what.
supermicro advertises 1.35v / 1.5v compatibility but
1.35v has not worked consistently for me at all

#### hangs on BIOS if any connected drive has corrupted boot
i had an ssd connected with a windows 10 install that borked itself,
and that inadvertently caused the BIOS to become completely inaccessible
until i figured out that i needed to disconnect the disk...
the same thing happened when i left an install usb key plugged in???

### so many RMAs ughhhh

1st rma was for not powering on when shorting the power pins, replaced. 2nd rma supposedly repaired a black screen issue / phantom overheat, and, well... it no longer trips the JOH1 header but it still has the black screen issue. after some amount of time the system hangs and the video output goes to "no signal". it seems to work for a few hours, then it doesn't work for like a full day, then it starts working again after you leave it alone for a while, ad nauseum. 

- is it the psu?
- is it the cpu1 pins?
- is it the ethernet?
- is it the ssd?

~who even knows~ seems to be that evga nex750g power supply, because it's not happening with a new evga 500bq... also apparently you can test supermicro mobos without the cpu1/2 pins -- those are just used for delivering more power when needed. thanks to being told this by a rep, i was able to test more psus and discover this. so i guess now i need to buy a new/working psu with dual eps. ugh. also presumably the ram actually works, but i haven't tested all 16 sticks with a working psu.
