+++
title = "Notifying remote servers that they should refetch an object"
date = "2019-11-01"
+++

{{toc}}

## Preserved text

<https://socialhub.activitypub.rocks/t/notifying-remote-servers-that-they-should-refetch-an-object/259>

@trwnh:

[quote="trwnh, post:1, topic:259, full:true"]
S2S Update:

> 7.3 Update Activity
>
> For server to server interactions, an `Update` activity means that the receiving server *SHOULD* update its copy of the `object` of the same `id` to the copy supplied in the `Update` activity. Unlike the [ client to server handling of the Update activity ](https://www.w3.org/TR/activitypub/#update-activity-outbox), this is not a partial update but a complete replacement of the object.
>
> The receiving server *MUST* take care to be sure that the `Update` is authorized to modify its `object` . At minimum, this may be done by ensuring that the `Update` and its `object` are of same origin.

Presumably sending something like this over S2S would just get the object replaced by an empty copy:

```
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "https://example.com/activities/1",
  "type": "Update",
  "actor": "https://example.com/actors/1",
  "object": "https://example.com/objects/1"
}
```

So how would this update/refetch notification be done properly?
[/quote]

---

@nightpool:

wait, what? why would the example posted not be compliant? The two representations (including an IRI to dereference vs including an embedded object) should be identical on the JSON-LD level.

I agree that the spec might expect you to inline the object, but I absolutely disagree that it requires it.

---

@kaniini:

I agree with nightpool, a bare IRI is completely fine here.

---

@trwnh:

OK, then. If a bare IRI is fine, then the next question would probably be how this would be handled in existing implementations. If an ActivityPub implementation were to hypothetically use Update in this manner, would it be received and handled properly?

## Current thoughts

needs testing or review, but should be possible imo. and if it's possible then maybe it could be expected?