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

## distribution model

[tldr i should choose if i want my reply to go to my followers, or their followers, or just them, or all of the above, or whatever]

direct addressing should be better supported

alice makes a post and sends it to her followers or whatever. optionally it can be made available to the public?

```yaml
id: alice.example/54078934249073290847321094/activity/1
actor: alice.example
type: Create
object:
  id: alice.example/54078934249073290847321094
  type: Note
  content: "hello world"
  audience: Public  # could be alice.example/followers instead
cc: [alice.example/followers, Public]
```

bob replies to alice, they send that to alice only, and *maybe* optionally to their followers. **this should be up to the user (bob).**

```yaml
id: bob.example/94378256423895476238954/activity/1
actor: bob.example
type: Create
object:
  id: bob.example/94378256423895476238954
  type: Note
  inReplyTo: alice.example/54078934249073290847321094
  content: "hi i'm bob"
  audience: Public
to: [alice.example]
cc: [alice.example/followers, bob.example/followers]  # alice's followers will be inbox-forwarded if alice allows it
```

alice can then choose to redistribute that reply. **this should be up to the recipient (alice)** and also **this should be made clear to the replier (bob)**

```yaml
id: alice.example/7659804769058743906/activity/1
actor: alice.example
type: Announce
object: bob.example/94378256423895476238954
cc: [alice.example/followers, bob.example]
```

note that alice might allow bob's replies to be automatically redistributed, if alice trusts bob.

bob may not want his reply to be redistributed though

```yaml
id: bob.example/15789436589346/activity/1
actor: bob.example
type: Create
object:
  id: bob.example/15789436589346  # may be excluded but could be better to explicitly specify as null. if provided, will return 401 Not Authorized if you do HTTP GET
  type: Note
  inReplyTo: alice.example/54078934249073290847321094
  content: "i'm gay. don't tell anyone"
  audience: alice.example
to: [alice.example]
```

alice can reply to bob's private reply, but no one else can see that reply

bob might also make his reply explicitly anonymous and transient by using id: null or not providing id

alice's reply SHOULD NOT target her followers, although she is capable of doing so. **this should be an explicit decision.**

### maybe objects shouldn't be inlined tho

spritely magenc golem ocappub etc etc etc

```yaml
id: alice.example/54078934249073290847321094/activity/1
actor: alice.example
type: Create
object: alice.example/54078934249073290847321094  # how to fetch this?
cc: [alice.example/followers, Public]
```