There are special collections that are managed by server side-effects, such as `followers` `following` `liked` on actors and possibly `replies`, `likes`, `shares` on objects. In most cases, the side-effects are the only intended way of interacting with these collections; for example, a Follow results in an Accept Follow, which has the side effect of adding them to `followers` and them adding you to `following`; a Like has the side effect of you adding the object to `liked` and them adding your Like to `likes`; an Announce has the side effect of them adding the Announce to `shares`, and a small exception for `replies` which may be managed manually.

But there's nothing in ActivityPub explicitly preventing a user from targeting their followers/following with an Add/Remove. In the case of a Remove, you can say that there is a legitimate use-case for this; perhaps you no longer wish to follow or be followed by a certain actor. However, for Add, it's a bit weirder -- you can Add an actor to your `followers` despite never receiving a Follow from them. You can add an actor to your `following` despite never receiving an Accept Follow from them.

There is some language that can be interpreted along these lines, in 6.5 Follow 

> The Follow activity is used to subscribe to the activities of another actor. [...] The side effect of receiving this in an outbox is that the server SHOULD add the object to the actor's [following Collection](https://www.w3.org/TR/activitypub/#following) when and only if an Accept activity is subsequently received with this Follow activity as its object.

and in 6.6/6.7 for Add/Remove:

> the server SHOULD add the object to the collection specified in the target property, unless: [...] the object is not allowed to be added to the target collection for some other reason, at the receiving server's discretion. 

Taken together, we conclude that the logical `iff` regarding Accept Follow is the only way to add the other actor to your following. A C2S Add/Remove would not be allowed due to "discretion".

Given this: how do we signal server management instead of actor management of these collections? My initial leaning would be to exclude `attributedTo` from the representation of these collections; in the absence of any attribution, it is assumed that the collection is owned by the server running at that origin / domain / hostname. (For S2S follower removal, this would have to be a special case handled similarly to Reject Follow being sent at any time after an Accept Follow, but that's a separate topic.)