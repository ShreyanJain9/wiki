+++
title = "Dereferencing non-HTTPS URIs as `id`"
date = "2019-10-11"
+++

{{toc}}

## Preserved text

<https://socialhub.activitypub.rocks/t/dereferencing-non-https-uris-as-id/116>

---

@trwnh:

> 3.1 Object Identifiers
>
> All Objects in [ActivityStreams] should have unique global identifiers. ActivityPub extends this requirement; all objects distributed by the ActivityPub protocol MUST have unique global identifiers, unless they are intentionally transient […] These identifiers must fall into one of the following groups:
>
> 1. Publicly dereferencable URIs, such as HTTPS URIs, with their authority belonging to that of their originating server. (Publicly facing content SHOULD use HTTPS URIs).
> 2. An ID explicitly specified as the JSON null object, which implies an anonymous object (a part of its parent context)
>
> Identifiers MUST be provided for activities posted in server to server communication, unless the activity is intentionally transient. However, for client to server communication, a server receiving an object posted to the outbox with no specified id SHOULD allocate an object ID in the actor’s namespace and attach it to the posted object.
>
> All objects have the following properties:
>
> id
>
> The object’s unique global identifier (unless the object is transient, in which case the id MAY be omitted).
>
> type
>
> The type of the object.

One thing I've wondered about is point 1 under 3.1 -- the id MUST be publicly dereferencable, with its authority belonging to the origin server... but it only SHOULD be https.

So, does this mean that there are other possible choices for a publicly dereferencable id with proper authority, that *isn't* HTTPS? I assume that the intention was to suggest using HTTPS over HTTP (e.g. section 3.2 assumes HTTP GET and content negotiation, as well as headers and 403/404 error codes; section 5.1/5.2 specifies HTTP POST to outbox/inbox; Section 6 uses MUST for making an HTTP POST to actor outboxes and 201 codes), but my curiosity is with other URI schemes entirely.

Perhaps one practical consideration (as there are various impractical ones, such as file://, ftp://, ftps://, sftp://, and so on) is to not use a URL but instead use a URN (such as doi, isbn, and other urn: URIs). Of course, this requires us to do a little more work to treat them as "dereferencable", such as by including a HTTPS proxy or some other resolver service. I wonder how that might be done, and whether this is at all worth pursuing. It could be used for deduplication, for example, by assigning a network-wide URN, with the authority deferred to the lookup service/proxy used as an instrument.

---

@cwebber:

> So, does this mean that there are other possible choices for a publicly dereferencable id with proper authority, that isn’t HTTPS?

Yes and the [Golem demo](https://gitlab.com/spritely/golem/blob/master/README.org) shows exactly an example of this, using a (very-cut-down demo) of using [Datashards](https://datashards.net/) with ActivityPub (it was called “magenc” back then) to distribute activities. A similar example could be done with IPFS, for instance (though that doesn’t provide the privacy/security properties that Datashards does).

Similarly, [bearcaps](https://github.com/cwebber/rwot9-prague/blob/bearcaps/topics-and-advance-readings/bearcaps.md) are a possible non-https URI scheme that we might use.

[...] Wanting to support Tor Onion Services is a reason I explicitly pushed back against pressure to make the spec https-only, which some people wanted.

---

@trwnh:

I guess what I’m trying to comprehend the most would be, how would compatibility work between the HTTPS linked-data web, and the non-HTTPS documents? If we start passing around AP JSON-LD documents with non-HTTPS `id` then it would obviously break due to basically all implementations assuming that all they need to do is HTTP GET `id`.

I remember reading the [RWoT paper about DIDs in ActivityPub](https://github.com/WebOfTrustInfo/rwot5-boston/blob/master/final-documents/activitypub-decentralized-distributed.pdf) and having the same confusion at the time about how it would work practically. The closest thing I could find was [`instrument`](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-instrument) in the AS2 Vocab, but that has a domain of `Activity` only, and is described as "Identifies one or more objects used (or to be used) in the completion of an `Activity`", so it would have to be redefined to work on `Object` as well.

---

@rinpatch:

Wouldn't an array of `id` work?

---

@trwnh:

Actually, multiple `id` seems interesting to me for a different thing: nomadic identity. Consider a case where an Object has multiple HTTPS URIs.

That might not be the best way to do it, though. In fact, one shortcoming that is immediately apparent to me is the question of what to do if the document retrieved from one `id` is different from that retrieved from another `id`. So it would be better to have one canonical `id` only, where possible. But technically, the problem of differing documents can still happen, due to something as simple as a desync.

[[future errata: this is what `alsoKnownAs` could be used for?]]

I’d really like to focus on modeling how to dereference non-HTTPS in a way that is still roughly compatible with the HTTPS Web-based network. So far the following options have been mentioned:

* `id` as Array; pick whichever one you understand.
* `url` as Array; pick whichever one you understand but leave `id` as HTTPS.
* just break compat and let implementations figure out how to resolve `id` (if they can)
* use a local proxy directly as `id` (technically still globally unique but represents change of authority)
* use a proxy Service as `instrument` (and extend `instrument` to be applied to `Object` and not just `Activity`)

---

@how:

I’m a bit concerned about extending to objects what could be done with creating an object-specifc actor acting as a proxy. The `instrument` service seems to be adapted to this use-case: you get a representation of the object’s metadata but must use the out of band service to retrieve the actual object. Best of both worlds?

---

@nightpool:

The ActivityPub standard does not define that any given server must support any one scheme. I don’t understand what the purpose of having it do so would be? it would just limit the flexibility of the protocol for no practical benefit (If two servers don’t support the same schemes, then they’re obviously not going to be able to talk to each other, MUST or no MUST)

---

@trwnh:

I think there’s probably mixing of concerns to treat non-HTTPS as primarily an issue of nomadic content; while there is *potential* application toward this, I think the incompatibility issue is probably more pressing in the long run. You can claim that a software is “compatible with ActivityPub”, but this is a statement that needs clarification and qualification. And this is also something that would become even more of an issue if ActivityPub is implemented via networks other than HTTP(S).

In that sense, there is no “ActivityPub network” – there is only currently an “ActivityPub over HTTPS network” (using HTTPS ids), as well as a sub-network of “ActivityPub over HTTPS network, with WebFinger” (perhaps more colloquially referred to as “the Mastodon network”).

Even if every (or the majority of) software implementation(s) moves to adopt `bear:` URIs as `id`, there is now still the question of which URI schemes are supported with the `u` parameter of the bearcap. The draft for bearcaps defines `u` as “The stable URL that the bearer capability URI is associated with for requests”, which is, again, not specifically HTTP(S). But even then, specifying a “stable URL” means that we are precluding URNs as a form of reference.

As nightpool mentions:

> If two servers don’t support the same schemes, then they’re obviously not going to be able to talk to each other

So I guess my main concern is that so-called “ActivityPub” software will settle around the lowest common denominator or the most restrictive set of requirements for interoperability. I’d like to lessen that if possible, so that individual implementations have the option to explore alternative lookup strategies for other “publicly dereferencable” schemes without that implying a choice between “hard break in network compatibility” or “stuck using HTTPS for `id` because everyone else is”. Although a hard break might be desirable in some cases, e.g. “if you don’t understand bearcaps then you can’t interact with this object”.

---

## current thoughts

we've well and truly collapsed into the "https only" world by now. any attempt to use a different scheme represents a sort of netsplit. this is not all good or bad though; it just means that the only network we have is the World Wide Web.

`alsoKnownAs` may be useful here as well: <https://socialhub.activitypub.rocks/t/defining-alsoknownas/907/30>

> i see a few possible approaches:
> 
> 1. defer `alsoKnownAs` to mastodon's usage (concept 2, controlling the listed actor). define a new term `aliases` (pending name) to represent concept 1, different identifiers for the same subject. we might also define `subject` or some similar term to identify a "canonical" identifier?
> 
> 1. defer `alsoKnownAs` to the DID core definition (concept 1, different identifiers for the same subject). implement a transitional period in which mastodon and other projects switch to a different mechanism more along the lines of rel-me. eventually deprecate the use of `alsoKnownAs` for determining rel-me.

aside from that, the `instrument` idea still has some potential