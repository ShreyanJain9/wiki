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

HOWEVER: in practice, you will probably need to be able to handle both types in both request+response

impls that return activity+json:

- mastodon
- pleroma

impls that return ld+json:

- misskey
- pixelfed