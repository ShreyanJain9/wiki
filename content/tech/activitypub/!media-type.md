+++
+++

{{<toc>}}

## interpretation of request/response types in as2/ap

AS2-Core says

> An Activity Streams Document is a JSON document whose root value is an Activity Streams Object of any type [...] and whose MIME media type is `application/activity+json` [...] Implementations SHOULD consider the `application/ld+json; profile="https://www.w3.org/ns/activitystreams"` media type as being equivalent to `application/activity+json`.

AP says

> POST requests (eg. to the inbox) MUST be made with a Content-Type of `application/ld+json; profile="https://www.w3.org/ns/activitystreams"` and GET requests with an Accept header of `application/ld+json; profile="https://www.w3.org/ns/activitystreams"`. Servers SHOULD interpret a Content-Type or Accept header of `application/activity+json` as equivalent

> MUST present the ActivityStreams object representation in response to `application/ld+json; profile="https://www.w3.org/ns/activitystreams"`, and SHOULD also present the ActivityStreams representation in response to `application/activity+json` as well. The client MUST specify an Accept header with the `application/ld+json; profile="https://www.w3.org/ns/activitystreams"` media type in order to retrieve the activity

basically as2 prefers activity+json while ap prefers ld+json with the activitystreams profile. ap is an extension to as2 so i guess ap takes precedence here...

taken literally and strictly, i interpret it like so:

- ap must request ld+json
- old as2 may request activity+json

and on the response side:

- return activity+json (regardless of request type)

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

## QUIRK: multiple types

ALSO NOTE: you may encounter multiple accept/content-type values. mastodon for example makes GET requests with `Accept: application/activity+json, application/ld+json` (in violation of the activitypub spec) or `Accept: application/activity+json, application/ld+json; profile="https://www.w3.org/ns/activitystreams", text/html;q=0.1` (also in violation of activitypub). these are still valid by RFC 9110 "HTTP Semantics" `Accept:` header parsing rules. note that multiple types are separated by a comma (`,`) but you must not parse them in order; *you parse them by most specific type first*.

> for example:
> `Accept: text/*, text/plain, text/plain;format=flowed, */*`
> [has] the following precedence:
> 1. text/plain;format=flowed
> 2. text/plain
> 3. text/*
> 4. \*/\*

