https://mastodon.social/@trwnh/109965906891374818

we have a domain name system but why don't we have a domain number system

just like we can register human-friendly names, we should be able to register persistent machine-friendly numbers to websites and domains. the name and number are aliased together, or we map name => number => ip address

yes this idea is copied from xri i-names and i-numbers, it's one of the best ideas to come out of that effort (aside from delegation of authority at any point in the identifier)

the world if xri took off

future.jpg

i've talked/thought about doing something similar with webfinger, but the "authority delegation" bit is really hard without a persistent unchanging identifier in some common authoritative namespace. you can defer to some other DNS domain's webfinger endpoint, but that doesn't get rid of the fragility in expiring or reassigning domain names.

it's interesting that bluesky comes to a similar conclusion with its "placeholder" DID scheme, did:plc: -- identifiers are centralized around a single "PLC server". the DNS stuff maps to a did:plc: that gets resolved by the centralized resolution server.

we could do something similar with a centralized server/domain that did nothing but assign identifiers and map to current location... of course, that doesn't solve the problem, it just shifts the burden.

so here's a crackpot idea that requires "minimal" changes to the way the world works

1. register a TLD with the IANA/ICANN -- let's call it something like .uuid
2. allow anyone to register UUIDs on this TLD -- this should be as easy as registering any other domain name, but instead of getting to pick the name, it's auto-assigned to you
3. CNAME your desired public-facing domain name to the .uuid domain you registered
4. optionally: run some software to "reverse map" your .uuid URIs (Webfinger)

you could also use plain old HTTP redirects instead of Webfinger, if you trusted the thing at the other end to stay online

but the main thing would be being able to use

trwnh.com/some-resource => 05517367-0a6a-42c1-9810-9fcf264a505b.uuid/some-resource

and then later change trwnh.com to some other domain

any software/application dealing with identifiers SHOULD support using the "canonical" identifier, so you can signal that the .uuid-assigned URI is the canonical one

crucially, the .uuid registry would never expire, and probably it should be free. the goal is to never let the .uuid registration lapse, ever

maybe your domain name registrar could even host a reverse-mapping server for you, if they wanted to expand their services?

also something to consider: under what circumstances might an entire TLD lapse? like this is super unlikely, sure, but what happens if the entity behind some TLD in the IANA root zone database goes insolvent?