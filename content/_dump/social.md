is2g ifwhen i make my thing i am going to put a lot of effort into a filter system that actually makes sense. i will not be supporting "mute" or "block" as binary features, but rather, everything flows through the filter system. mastodon filter v2 system is an improvement but doesn't go far enough

---

in any case yes it would be better to encrypt everything or just not peer/replicate at all. fedi has a problem with this because for some unknown reason we all decided it would be a convoluted distributed state machine which is a bad idea when you don't trust all participants, and also worse when those participants are explicitly redistributing it while attributed to you. at that point you might as well go all-in on syndication rather than federation. put another way: federation makes sense for message passing but not for publishing (and fedi is an awkward and unclear hybrid between messaging and publishing)

---

idle thought: idk if i care so much about "fedi" as much as it is "people who are on fedi". either in the abstract or in the concrete, i just wanna make friends and talk to cool people. fedi is little more than a place for fateful encounters based on the streams of consciousness we put out there. the important thing is that no one meddles with this for profit or otherwise. the second fedi stops providing that, i'm out.

it's a big part of why i don't really relate or sympathize with people who just want fedi to grow. i think any growth has to be sustainable. the values are valuable. if the values are compromised then fedi isn't valuable to me anymore.

note that there's a big distinction between this and communicating with people you know. if some people are only on fedi then that has a different type of value. network value is not the same as the value of the network.

it's always the people, i'm only here for the people. i do not want to keep track of the diplomatic politics of tens of thousands of nation-states. no one should! we made a wrong turn somewhere back in 2017 when people started making "community" instances, and arguably we were on the wrong path from the very beginning. maybe the whole thing is wrong tbh. we need to go back to actual forums. the ergonomics are all wrong. the model is wrong. we're just trying to mash everything roughly into the shape of twitter. and worse, every twitter has its own culture and community and is assumed to be homogeneous! how do you atomize all this? 

---

[asking natalie what should be removed or what is missing from misskey](https://mastodon.social/@trwnh/109958119755489250)

<blockquote>

i like that it's an outsider take on fedi and is not afraid to push boundaries and go beyond what mastodong has envisioned

what's missing is muting/blocking actually being functional. i'd also like to see Room come back lol

what should be removed: basically nothing. maybe proxy accounts. if i wanted minimal i'd go use some useless software like honk, misskey maximalism is actually really fun

</blockquote>

---

the general objection is "that's not how we currently federate", as if the way we currently federate should be preserved forever

---

"relays" on fedi are like the worst possible interpretation of relays. imagine one big irc room that has no topic whatsoever. just thousands of people posting whatever they want. why

also imagine your irc client had autologging turned on, and didn't have an easy way to clear specific logs

---

this shit should be generic and it can be browsed by a social web browser or a linked data renderer

i think it makes the most sense to lean into the Web and just focus on making the publishing as easy and straightforward as possible. i think most people (ideologues aside) don't actually want "federation" as commonly thought of. in absolute terms they want their posts to show up in a reader.

there still is a place for federation, the "federation" i'm interested in is more like replication in a distributed database. and i'd like to link it all up across domains like "nomadic identity".

example: imagine a network of generic servers

your server shuts down, you walk up to any server that has your posts already, prove your identity, pick up like nothing happened

basically you should be able to "claim" your federated profile on other domains, kind of like how on a centralized site you can claim your profile page if you're a celebrity or whatever

nomadic identity is literally just doing this process yourself beforehand by setting up mirrors/replicas of your key

---

i forget who it was that said "a single timeline is unsustainable" (aaron parecki?) but i'm feeling more like the real cardinal sin is publishing to a single profile. i don't mind *reading* everything in a single view, although making lists certainly helps. it's the *publishing* that annoys me. i almost *never* want to send a post to *all* of my followers. and this goes doubly for replies. i often want to reply within a specific context and only *optionally* tell my followers about it.

"one account per feed" is a goddamn travesty. an account should be able to have multiple profiles, and a profile should be able to have multiple feeds, and you should be able to follow individual feeds instead of being forced to follow an entire profile.

yeah, i was thinking similarly. except i was going to make Collections into actors that could be followed. which would be incompatible with mastodon, because mastodon assumes wrongly that actors must be exactly one of The Five Types. but then again, i am somewhat losing interest in this whole "social media" thing and would prefer to focus on a purer model of publishing web resources and managing them with collections. so maybe "mastodon compatibility" is not all it's cracked up to be...

if anything, i was thinking of treating "mastodon compatibility" similar to a crossposter up until such point that mastodon relaxes or removes its unnecessary type-checking. you'd have an actor that you can send your activities to, and it will translate them into mastodon-flavoured activitypub and deliver out that translated copy. this actor would have its preferredUsername and all that compat stuff.

---

btw i think that deletes being so costly is something that is entirely a consequence of having essentially replicated the data with a ttl of forever. that's the default mode of every "fedi" software. it makes no sense for most people and it stems from the ideological decisions of over a decade ago

by which i mean: there's a word for having your published content be replicated to other sites, and it's "syndication". we had this technology for really simple syndication... rss. it later evolved into atom. the "fediverse" was founded on ostatus built on atom feeds. when we say we're "federating" we're really more accurately "syndicating". you just don't have control over the accounts being created on other domains, on your behalf.

i keep talking about how the metaphors are wrong, like how we merged the concepts of publishing and messaging to the detriment of both. we're sending messages that contain the entirety of a resource that we meant to be published. of course it's going to lead to outcomes that don't make sense. maybe one day people will understand this

also... authorized fetch won't solve this. if you have a password protected blog, and someone is saving every new post and republishing it, and you let this happen continually, then what's the point of the password?

---

i think it's very important that users be able to understand and reason about the software that they are using! when it "does the thing", it should do so predictably.

the argument for generic AP is that generic AP allows you to manage resources predictably, without having to care what others will do. it's a standardized storage layer. without it, you need a mastodon account, a pixelfed account, etc... every "platform" owns your data separately.

imagine if switching from outlook to thunderbird necessitated losing all your emails and maybe even your contacts.