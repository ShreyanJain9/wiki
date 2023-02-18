+++
title = "interpretation of request/response media types in as2/ap"
autonumbering = true
+++

AS2-Core says

> An Activity Streams Document is a JSON document whose root value is an Activity Streams Object of any type [...] and whose MIME media type is `application/activity+json` [...] Implementations SHOULD consider the `application/ld+json; profile="https://www.w3.org/ns/activitystreams"` media type as being equivalent to `application/activity+json`.

AP says

> POST requests (eg. to the inbox) MUST be made with a Content-Type of `application/ld+json; profile="https://www.w3.org/ns/activitystreams"` and GET requests with an Accept header of `application/ld+json; profile="https://www.w3.org/ns/activitystreams"`. Servers SHOULD interpret a Content-Type or Accept header of `application/activity+json` as equivalent

> MUST present the ActivityStreams object representation in response to `application/ld+json; profile="https://www.w3.org/ns/activitystreams"`, and SHOULD also present the ActivityStreams representation in response to `application/activity+json` as well. The client MUST specify an Accept header with the `application/ld+json; profile="https://www.w3.org/ns/activitystreams"` media type in order to retrieve the activity

basically as2 prefers activity+json while ap prefers ld+json with the activitystreams profile. ap is an extension to as2 so i guess ap takes precedence here...

{{<toc>}}

## conclusion A: request ld+json, respond activity+json?

taken literally and strictly, i interpret it like so:

- ap must request ld+json
- old as2 may request activity+json

and on the response side:

- return activity+json regardless of request type? since as2-core specifies that type for an "activity streams document"
  - alternatively, return whatever type was requested. this is less likely to break expectations or otherwise false assumptions.

## conclusion B: the semantics of json-ld profiles

you could also interpret a semantic difference between the two?

ld+json registration has 6 defined profiles: <https://www.iana.org/assignments/media-types/application/ld+json>

4 of these are the different forms of json-ld

- `http://www.w3.org/ns/json-ld#expanded` to request or respond with data that has been expanded
- `http://www.w3.org/ns/json-ld#compacted` to request or respond with data that has been compacted
- `http://www.w3.org/ns/json-ld#flattened` to request or respond with data that has been flattened
- `http://www.w3.org/ns/json-ld#framed` to request or respond with data that has been framed

there are also 2 utility profiles

- `http://www.w3.org/ns/json-ld#frame` to request or respond with a frame (that can be applied to data)
- `http://www.w3.org/ns/json-ld#context` to request or respond with a context document

so then, what is the activitystreams profile? it's not explicitly defined in either as2-core or in ap. but we can assume it has something to do with the extra restrictions and requirements that as2 adds on top of json-ld -- that the document must be compacted against the activitystreams context at minimum.

### @context may be missing in activity+json?

there's also this bit from as2-core:

>  When a JSON-LD enabled Activity Streams 2.0 implementation encounters a JSON document identified using the `application/activity+json` MIME media type, and that document does not contain a @context property whose value includes a reference to the normative Activity Streams 2.0 JSON-LD @context definition, the implementation MUST assume that the normative @context definition still applies.

so basically ld-aware as2 implementations MUST inject the as2 context if it is missing from activity+json. but the reverse implication is what's interesting here -- activity+json means that context isn't required!

also from as2-core:

> Implementations producing Activity Streams 2.0 documents SHOULD include a @context property with a value that includes a reference to the normative Activity Streams 2.0 JSON-LD @context definition using the URL `https://www.w3.org/ns/activitystreams`.

note that this is only a SHOULD.

as2 author james snell explains further: <https://github.com/w3c/activitystreams/issues/132#issuecomment-106854283>

> You can still use `application/ld+json` if you wish. The `application/activity+json` media type implies a very specific profile of JSON-LD -- that is, JSON-LD in compacted form using the normative context definition. When using `application/activity+json`, the context can be implicit if you're not using any extensions.

further comments from the issue:

> AS2 is not just JSON-LD, it's JSON-LD plus a number of additional constraints. The `application/activity+json` media type is used to indicate that those additional constraints are in effect. Yes, you can still treat it as straightforward JSON-LD, and yes, it's best if implementers make the @context explicit in order to maximize interoperability with existing JSON-LD stacks as well as performance, but when the document is served up as `application/activity+json`, there are certain assumptions that can be made.

markus lanthaler concludes:

>> Yes, you can still treat it as straightforward JSON-LD
>
> No, you can't if there's no explicit context.

### supporting generic json-ld consumers is a choice

it can be said that plain-json as2-native software may generate activity+json documents that are not compatible with json-ld. this gives as2 the dubious ability to be "json only".

it can also be said that generic json-ld consumers may not recognize the activity+json type and may strictly require ld+json... although they shouldn't. but they might.

so, the real problem is for generic json-ld software which has no idea what activitystreams is. to play nice with this kind of software, as2-core says you SHOULD include the as2 context. but this ends up being a choice because it's only a SHOULD and not a MUST.

aside from that, the whole type debate is largely irrelevant for software which is activity-aware or activity-native (since you are recommended to treat the two as equivalent).

## both are equivalent (and used in practice!)

HOWEVER: in practice, you will probably need to be able to handle both types in both request+response

impls that GET with activity+json:

- ...

impls that GET with ld+json:

- ...

impls that POST with activity+json:

- mastodon

impls that POST with ld+json:

- pixelfed

impls that return activity+json:

- mastodon
- pleroma

impls that return ld+json:

- misskey
- pixelfed

### QUIRK: multiple types

ALSO NOTE: you may encounter multiple accept/content-type values. mastodon for example makes GET requests with `Accept: application/activity+json, application/ld+json` (in violation of the activitypub spec) or `Accept: application/activity+json, application/ld+json; profile="https://www.w3.org/ns/activitystreams", text/html;q=0.1` (also in violation of activitypub). these are still valid by RFC 9110 "HTTP Semantics" `Accept:` header parsing rules. note that multiple types are separated by a comma (`,`) but you must not parse them in order; *you parse them by most specific type first*.

> for example:
> `Accept: text/*, text/plain, text/plain;format=flowed, */*`
> [has] the following precedence:
> 1. text/plain;format=flowed
> 2. text/plain
> 3. text/*
> 4. \*/\*

fwiw mastodon doesn't seem interested in changing this so you will have to Deal With It if you care about mastodon compat <https://github.com/mastodon/mastodon/pull/22722>

## see also

- SocialWG: Media type for AS2 <https://www.w3.org/wiki/Socialwg/Media_type_for_AS2> -- a discussion from when AS2 was being written.