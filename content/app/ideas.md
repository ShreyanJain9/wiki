# Ideas for extensions and differences from current implementations

## Webfinger Content-Type of activitystreams profile

i wonder if setting a Content-Type of `"application/ld+json; profile=\"https://www.w3.org/ns/activitystreams\""` instead of `"application/activity+json"` would break anything

## Message type

Message is an IntransitiveActivity?

```yaml
id: https://alice.social/activities/1
actor: https://alice.social
type: Message
to: https://bob.social
content: "hello bob"
```

could also be a regular Activity, where `object` is the conversation or room?

```yaml
id: https://alice.social/activities/1
actor: https://alice.social
type: Message
object: https://alice.social/rooms/1
to: https://alice.social/rooms/1/audience
content: "hello room"
```

instead of making it transitive, we could swap `object` for `context` which is a bit more semantically correct if pointing to a collection?

```yaml
id: https://alice.social/activities/1
actor: https://alice.social
type: Message
to: https://alice.social/rooms/1/audience
content: "hello room"
context: https://alice.social/rooms/1
```

or have both `object` and `context`?

```yaml
id: https://alice.social/activities/1
actor: https://alice.social
type: Message
object: https://alice.social/rooms/1
to: https://alice.social/rooms/1/audience
content: "hello room"
context: https://alice.social/rooms/1/context
```