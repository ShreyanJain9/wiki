https://mastodon.social/@trwnh/110361352685140966

so we might reasonably recreate bluesky's "placeholder" nameserver by having a well-known endpoint for resolving identifiers to as2 documents (with possibly a layer of indirection, in which case webfinger would probably work)

this basically replaces DNS for resolution. you'd just have to figure out how to assign ids in a distributed way without conflicts

for that matter: why isn't xrpc a .well-known endpoint? it absolutely should have been

i'm thinking if we wanted to reify webfinger and identity providers, we could just have identifiers assigned under the authority of webfinger subjects. where is the data? idk, check the current links via webfinger.

problem with that is, your IdP better be long-lived... in fact, it makes sense for a persistent IdP to last basically "forever" and just point to your current data stores. and then i guess you'd want IdP federation to ease migration between IdPs? or otherwise conflict-free id minting

tldr the open problems:

- how to assign ids without conflict? (preferably without dns or key based authority, so you can change those later)

- how to resolve those ids across multiple servers without tampering? (see also: dns poisoning and authoritative nameservers)

there's a lot of prior art in DNS and you'd be moving it all down to the HTTP level over webfinger and oidc and related stuff

basically instead of resolving names -> authoritative DNS nameservers -> IP addresses, you have to resolve uh... names -> authoritative Webfinger servers -> http(s) links

so i guess we need an equivalent to DNSSEC but for WebFinger

---

erincandescent writes:

<blockquote>

hash of public key; hash of first version of document (in chain), like they did. many decent options!

the did:plc approach allows you to rotate keys because your current identity document will eventually chain back to the original one which hashes to the value of your account ID

the bit the central server is doing in this system is - basically - ordering things & letting you use your recovery key to oblitterate any updates from your primary key from the last 72 hours (for if your primary key was compromised)

the key difficulty without any central server is timestamping things to ensure & validate ordering

</blockquote>

cobaltvelvet writes:

<blockquote>

i think i just like the idea of a minimal relay as this resolver, that would keep the least state possible inside it, so it can be swapped in and out easily, maybe even transparently so by your instance. but it would always need A Way to validate the 'latest location', either the keypairs that arent very securely held atm but instances already have, or a third secret thing (a dedicated channel, like making the resolver email you to confirm a migration, or another layer of signatures)

if i had to point to an inspiration, i think bittorrent trackers are great, easily replceable by a DHT, but also easy to just use two for redundency . ofc they don't really have to bother with any validation, but your instance can

</blockquote>

---

i'd just need to think through how to get consistency between different webfinger services, so that they agree on the response and serve you up the same JRD from some decentralized/distributed data store