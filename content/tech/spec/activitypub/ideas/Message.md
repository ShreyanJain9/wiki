## Message type

Message is an IntransitiveActivity?

```yaml
id: https://alice.social/activities/1
actor: https://alice.social
type: Message
to: https://bob.social
content: "hello bob"
```

representing rooms with context:

```yaml
id: https://alice.social/activities/1
actor: https://alice.social
type: Message
to: https://rooms.social/rooms/1/audience  # inbox forwarding, probably
content: "hello room"
context: https://rooms.social/rooms/1  # verify inclusion how?
```

making the context into an actor:

```yaml
id: https://alice.social/activities/1
actor: https://alice.social
type: Message
to: # inbox forwarding, definitely
  - https://rooms.social/rooms/1  # we need to deliver to the room actor
  - https://rooms.social/rooms/1/followers  # rooms.social or the room actor's client will forward to participants
content: "hello room"
context: https://rooms.social/rooms/1
```

use of bto/bcc?

```yaml
id: https://alice.social/activities/1
actor: https://alice.social
type: Message
content: "hello room"  # arguably only the content needs to be signed? no need for canonicalization
context: https://rooms.social/rooms/1
```