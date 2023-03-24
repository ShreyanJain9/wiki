+++
title = "Unresolved issues surrounding Follow activities"
date = "2019-10-10"
+++

{{<toc>}}

## Preserved text

<https://socialhub.activitypub.rocks/t/unresolved-issues-surrounding-follow-activities/114>

<article>
# First, some context…

What the C2S spec says:

> 6.5 Follow Activity
>
>
>
> The Follow activity is used to subscribe to the activities of another actor.
>
>
>
> The side effect of receiving this in an outbox is that the server SHOULD add the object to the actor’s following Collection when and only if an Accept activity is subsequently received with this Follow activity as its object.

What the S2S spec says:

> 7.5 Follow Activity
>
>
>
> The side effect of receiving this in an **inbox** is that the server *SHOULD* generate either an `Accept` or `Reject` activity with the Follow as the `object` and deliver it to the `actor` of the Follow. The `Accept` or `Reject` *MAY* be generated automatically, or *MAY* be the result of user input (possibly after some delay in which the user reviews). Servers *MAY* choose to not explicitly send a `Reject` in response to a `Follow` , though implementors ought to be aware that the server sending the request could be left in an intermediate state. For example, a server might not send a `Reject` to protect a user’s privacy.
>
>
>
> In the case of receiving an `Accept` referencing this `Follow` as the object, the server *SHOULD* add the `actor` to the object actor’s [Followers Collection ](https://www.w3.org/TR/activitypub/#followers). In the case of a `Reject` , the server *MUST NOT* add the actor to the object actor’s [Followers Collection ](https://www.w3.org/TR/activitypub/#followers).
> > Note
> >
> >
> >
> > Sometimes a successful `Follow` subscription may occur but at some future point delivery to the follower fails for an extended period of time. Implementations should be aware that there is no guarantee that actors on the network will remain reachable and should implement accordingly. For instance, if attempting to deliver to an actor for perhaps six months while the follower remains unreachable, it is reasonable that the delivering server remove the subscriber from the `followers` list. Timeframes and behavior for dealing with unreachable actors are left to the discretion of the delivering server.

Special collections:

> 5.3 Followers Collection
>
>
>
> Every [actor ](https://www.w3.org/TR/activitypub/#actors) *SHOULD* have a `followers` collection. This is a list of everyone who has sent a [Follow](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-follow) activity for the actor, added as a [side effect](https://www.w3.org/TR/activitypub/#follow-activity-outbox). This is where one would find a list of all the actors that are following the actor. The `followers` collection *MUST* be either an [ `OrderedCollection` ](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-orderedcollection) or a [ `Collection` ](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-collection) and *MAY* be filtered on privileges of an authenticated user or as appropriate when no authentication is given.
> > Note: Default for notification targeting
> >
> >
> >
> > The follow activity generally is a request to see the objects an actor creates. This makes the Followers collection an appropriate default target for [delivery](https://www.w3.org/TR/activitypub/#delivery) of notifications.
>
>
> 5.4 Following Collection
>
>
>
> Every actor *SHOULD* have a `following` collection. This is a list of everybody that the actor has followed, added as a [side effect](https://www.w3.org/TR/activitypub/#follow-activity-outbox). The `following` collection *MUST* be either an [ `OrderedCollection` ](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-orderedcollection) or a [ `Collection` ](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-collection) and *MAY* be filtered on privileges of an authenticated user or as appropriate when no authentication is given.

In summary… C2S Follow puts a Follow activity in your outbox, your server waits for an Accept, then adds that actor to following. S2S Follow takes a Follow activity in your inbox, your server wraps it in Accept/Reject and delivers that. S2S Accept Follow adds that actor to followers. (All of this is SHOULD language.)

However, this leaves some things too ambiguous…

# Following only a *subset* of activities

By default, when you Follow an Actor, most implementations interpret that as a sign of interest in every single activity that the actor publishes, similar to the note in 5.3 (“The follow activity generally is a request to see the objects an actor creates.”). But there is no way to signal an interest in receiving only *some* of the activities. Perhaps the only possible concession here is the `streams` property of an actor, but there is basically nothing to guide implementations aside from a passing mention that is only 10 words long:

> **streams**
> A list of supplementary Collections which may be of interest.

This suggests a pull-based approach, where you can GET the `streams` of an actor… but you cannot have the actor deliver any activities from those streams. Nor is it explicitly defined whether these collections contain activities or objects, or what the collections are actually for (aside from heuristically guessing based on the name of the collection?) Perhaps there could be a way to consistently understand these collections and maybe follow just those streams instead of the actors, but that would require…

# Following non-actor objects

Per 6.5, “The Follow activity is used to subscribe to the activities of another actor.” But sometimes you don’t necessarily care what a single actor does with multiple objects, you care about what multiple actors are doing with a single object.

AFAIK some implementations interpret Follow Object to generally mean that the software will deliver any activities targeting that object to any actor that has expressed interest through the Follow Object activity. Perhaps this should be formalized, as the following are currently not agreed upon:

* Who should you send the Follow Object to? The `attributedTo` actor? Does following that object imply also following the actor?
* How should you keep track of followed objects? Should they be added to the `following` collection like actors? Should individual objects define a `followers` collection?
* How do you know if an object is followable? If you send a Follow Object to remote software, it may not understand it. Is the presence of a `followers` collection enough for this? Does it also need `attributedTo`? Some other method?

# Practical applications

As prior art, Google+ Collections come to mind. For each post made to Google+, a user could choose to address it to Public, or to selected users (like actors), or to a Circle (like a collection of actors), or to a Community (like a group), or to a Collection (like a category/stream). This could be seen as confusing since it wasn’t really well-explained to users what any of these concepts meant, and Google+ also used Circles both for addressing those users *and* for viewing posts from those users, whereas most other services have a separate Lists feature for viewing posts from a subset of users. In essence, users can categorize their posts at the same time as they choose who to address them to. Consider the following hypothetical documents:

```
{
"id": "https://social.example/actors/1",
"type": "Person",
"inbox": "https://social.example/inboxes/1",
"outbox": "https://social.example/outboxes/1",
"followers": "https://social.example/actors/1/followers",
"streams": ["https://social.example/collections/1"]
}
```

```
{
"id": "https://social.example/objects/1",
"type": "Note",
"content": "a simple note",
"attributedTo": "https://social.example/actors/1",
"followers": "https://social.example/objects/1/followers"
}
```

```
{
"id": "https://social.example/collections/1",
"type": "OrderedCollection",
"items": ["https://social.example/objects/1"],
"followers": "https://social.example/collections/1/followers"
}
```

```
{
"id": "https://social.example/activities/1",
"type": ["Create", "Add"],
"actor": "https://social.example/actors/1",
"object": "https://social.example/objects/1",
"target": "https://social.example/collections/1",
"to": 
  [
  "https://social.example/actors/1/followers",
  "https://social.example/objects/1/followers",
  "https://social.example/collections/1/followers"
  ]
,
"cc": ["as:Public"]
}
```

# Etc

Any comments, additions, etc. about this?

</article>

---

@trwnh:

I should also add some discarded approaches that I considered at one point while thinking about this but determined to be too problematic:

* interpreting a Follow as subscribing to multiple actors instead of just one
* multiple outboxes (although this might still be applicable to other problems, just not this one for now)
* using Profiles instead of Actors (though this could still be done, it would be unsupported in current implementations; it would however become possible to Follow Profile instead of Follow Actor)
* having objects just be made into actors, a la the concept of message passing in computer science (although this adds a lot of complexity because objects generally only need a small part of what Actor provides)
* hackily having multiple actors representing the same Person, but using them to create objects, then Announcing those from a more “central” actor (too complicated and unwieldy)

---

@trwnh:

Sure, but how should we discover this?

As stated above I went with `attributedTo` and the presence of a `followers` property, but perhaps it may be better to use an explicit endpoint like `inbox`?

---

@trwnh:

It’s almost 3 years later and I think I’ve come around on this a bit –

> As stated above I went with `attributedTo` and the presence of a `followers` property, but perhaps it may be better to use an explicit endpoint like `inbox`?

I think explicit is better than implicit. All that’s required for an Object to be an Actor is to have `inbox` and `outbox`. The only issue with current language in the spec is that

> The outbox stream contains activities the user has published,

and semantically speaking actors are not equivalent to users, nor is the attribution required to match the same actor (or even *be* an actor – `attributedTo` can include another activity, for example).

Where the previous approach fails is that the `followers` collection may not be included or published. The `followers` property is only a SHOULD and not a MUST.

For delivery of the Follow activity, we can refer to the guidance on delivery:

> The [inbox](https://www.w3.org/TR/activitypub/#inbox) is determined by first [ retrieving the target actor’s JSON-LD representation](https://www.w3.org/TR/activitypub/#retrieving-objects) and then looking up the `inbox` property. If a recipient is a `Collection` or `OrderedCollection`, then the server *MUST* dereference the collection (with the user’s credentials) and discover inboxes for each item in the collection. Servers *MUST* limit the number of layers of indirections through collections which will be performed, which *MAY* be one.

Which, again, implies that we should just attach `inbox` on anything followable. And if we do that, then we should/must attach `outbox` as well to make it an actor (although in practice this may not be strictly required – it is possible to model having an inbox but no outbox, and some implementations might do this already in apparent violation of the spec).

---

@macgirvin:

I do recall when Mastodon first implemented ActivityPub and some folks were still using OStatus. In this case the inbox was set to NULL. We implemented AP at exactly the same time and this threw exceptions in my code. Was going to file a bug, but technically speaking I think it’s spec compliant. The language states that an actor must have the “following properties” and these records had the required properties, except they weren’t de-reference-able. In any case I consider this to be a legal interpretation and have code to handle it appropriately.

A more serious issue is that we implement access permissions to several collections and these are not de-reference-able unless the requestor has permission to access the collection. The spec is incredibly weak where it comes to dealing with permissions, but I have every right to restrict access of a resource to anybody I desire - even if it means my software isn’t technically compliant. This makes the quoted section in your reply contentious. These are deliverable through the forward-to-owner sections referenced elsewhere in the spec so everything still works; but not all collections are de-reference-able by every actor that receives an activity — nor should they be.

---

@trwnh:

[quote="macgirvin, post:8, topic:114"]
In this case the inbox was set to NULL.
[/quote]

Hmm. My interpretation is that `inbox` and `outbox` properties MUST be provided, but not necessarily be public. I’m not sure whether it makes sense to set `inbox` to `null` explicitly; is that not the same as excluding it? In any case, the practical implication of the current network (“ActivityPub over HTTPS”, or as I’ve taken to calling it recently, “ActivityPub over HTTPS with Webfinger and HTTP Signatures” :stuck_out_tongue: ) is that one can simply POST to an inbox IRI and have it either return successfully or error out with a 405 Not Allowed (if federation is not implemented). I’d be curious to hear any objections or concerns regarding this.

[quote="macgirvin, post:8, topic:114"]
I have every right to restrict access of a resource to anybody I desire - even if it means my software isn’t technically compliant.
[/quote]

I’m not sure if it’s technically “incompliant” to do so, but for what it’s worth, Pixelfed for example provides `inbox` for actors (currently equivalent to users, but under this proposal, it doesn’t have to be), and this `inbox` only supports POST and not GET, for obvious reasons – federation works by POSTing to the `inbox` and GET is only needed for C2S fetching your own inbox with authorization. That doesn’t seem like a problem to me; if anything, it seems natural since the inbox is only for delivery. Setting `inbox: null` or leaving it out implies that the object is not an actor, since it does not have both `inbox` and `outbox` (the only two required properties for actors). Aside from this heuristic, there doesn’t seem to be any other meaning to being an actor. So I would agree that yes, access to resources may be restricted without authorization. This doesn’t seem like an issue, although I may be misunderstanding or overlooking something.

[quote="macgirvin, post:8, topic:114"]
This makes the quoted section in your reply contentious. These are deliverable through the forward-to-owner sections referenced elsewhere in the spec so everything still works; but not all collections are de-reference-able by every actor that receives an activity — nor should they be.
[/quote]

Inbox forwarding could perhaps be applicable, but not any more so than it already is.

To clarify, the assumed / proposed flow is something like this:

1. I GET an actor at `https://example.social/actors/1` and see that their `streams` advertises `https://example.social/collections/1`:

```
GET https://example.social/actors/1 HTTP/1.1
Accept: application/ld+json; profile="https://www.w3.org/ns/activitystreams"

{
"@context": [...],
"id": "https://example.social/actors/1",
"type": "Person",
"inbox": "https://example.social/inboxes/1",
"outbox": "https://example.social/outboxes/1",
"streams": ["https://example.social/collections/1"],
...
}
```

2. I want to `Follow` the `Collection` at `https://example.social/collections/1` for updates. (Let’s say this `Collection` is advertised within `streams` and represents some categorization of posts, similar to Wordpress categories or Google+ Collections. Its items contain some arbitrary objects like `Note`s that have been published within the category/stream.)
3. I perform `GET https://example.social/collections/1`, optionally with an HTTP Signature for my actor `https://ap.trwnh.com/actors/1`. The server responds with a `Collection` containing only the items I am authorized to see, based on the HTTP Signature attached. If no signature is attached, then only items with `audience: as:Public` will be returned (likewise for `to`/`cc`/etc if the server software uses those for addressing instead of `audience`).

```
GET https://example.social/collections/1 HTTP/1.1
Accept: application/ld+json; profile="https://www.w3.org/ns/activitystreams"
Signature: keyId="https://ap.trwnh.com/actors/1/publicKey",...

{
"@context": [...],
"id": "https://example.social/collections/1",
"type": "OrderedCollection",
"totalItems": 1312,
"first": "https://example.social/collections/1?page=1",
"last": "https://example.social/collections/1?page=132",
"inbox": "https://example.social/inboxes/1",
"outbox": "https://example.social/outboxes/2"
}
```

4. From this response, I parse for the `Collection`’s `inbox` property. If it does not have one, I assume it cannot be followed. If it does have one, then I assume I can send a `Follow` there. In this example, `https://example.social/collections/1` has `inbox: https://example.social/inboxes/1`. (This happens to be the inbox for `https://example.social/actors/1`, but it doesn’t have to be. This is an implementation detail.) To follow the stream for updates:

```
POST https://example.social/inboxes/1 HTTP/1.1
Content-Type: application/ld+json; profile="https://www.w3.org/ns/activitystreams"

{
"@context": [...],
"type": "Follow",
"actor": "https://ap.trwnh.com/actors/1",
"object":  "https://example.social/collections/1"
}
```

5. The remote server then responds as per the individual implementation’s logic, e.g. with an `Accept Follow`

This is of course open to different methods of access control, object capability delegation, etc. – the key point is that the followable object has an `inbox` to `POST` a `Follow`, which I prefer over heuristics like dereferencing `attributedTo` and looking for an `inbox` there. It is simpler to just provide the `inbox` directly. And you might as well provide an `outbox` too and make it a full-fledged actor, since being an actor doesn’t actually imply much. Neither `inbox` nor `outbox` has to be public, anyway – they just have to be there.

One other addendum is that, last I looked into this, I saw some discussion around Friendica and Zap having or planning support for `Follow Note`, but I was not able to find how this support was implemented, if it was.

---

@macgirvin:

Interesting. Yeah, that all make sense.

As I recall the rationale for inbox = null was a hint that an actor exists at this address, but it is only useful by some other protocol from ActivityPub. If you perform some alternate or broader scoped discovery (outside of AP) you might find that you can actually communicate with it.

We perform Follow Note and if I recall Ignore Note as internal activities to indicate you want to see (or not see) notifications for this thread in the future. We currently don’t federate these activities - they’re strictly for internal use. The object is always the top-level post in a thread as these platforms default to conversation/threaded display.

Oh and the comment about permissions wasn’t really about inbox/outbox but about recipient collections. We permit people to hide who is in their followers/following and circles/aspects collections if they don’t feel like sharing these with the world. These will return 403 if the owner doesn’t want anybody to see their contents. This affects the address de-duplication sections of the spec since these collections can only be expanded/iterated by their origin server. We do filter outbox contents based on the request http-signature but I think this is common practice.

---

@trwnh:

This may be off-topic but it’s worth pointing out that most projects I’m familiar with tend to respond with some valid JSON(-LD), but intentionally leaving out any details the author does not want visible.

* Mastodon’s “Hide your network” option will return an `OrderedCollection` with the only useful property being `totalItems`. This is because Mastodon’s philosophy is to hide only the contents of the collection while still revealing metadata (presumably out of some concern for signaling “trustworthiness”, which to me seems flawed, but I digress):

```
GET https://mastodon.social/users/trwnh/followers HTTP/1.1
Accept: application/activity+json

{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "https://mastodon.social/users/trwnh/followers",
  "type": "OrderedCollection",
  "totalItems": 2482
}
```

* Pixelfed currently does something similar, but intentionally “lies” and claims that there are 0 items while explicitly returning an empty array as the value:

```
GET https://pixelfed.social/users/trwnh/followers HTTP/1.1
Accept: application/activity+json

{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "https://pixelfed.social/users/trwnh/followers",
  "type": "OrderedCollectionPage",
  "totalItems": 0,
  "orderedItems": []
}
```

* Pleroma has separate toggles for hiding either the items or the count (or both). If the items are hidden, the `orderedItems` will simply not be present. If the count is hidden, the `totalItems` will simply not be present. If both are hidden, both properties will simply not be present. On a UX level, hiding the counts requires hiding the items, because hiding only the count leaves you open to manually counting the items yourself.

```
GET https://letsallovela.in/users/at/followers HTTP/1.1
Accept: application/activity+json

{
  "@context": [...],
  "first": "https://letsalllovela.in/users/at/followers?page=1",
  "id": "https://letsalllovela.in/users/at/followers",
  "type": "OrderedCollection"
}
```

* Misskey does the same thing as what you described: 403 Forbidden.

---

@trwnh:

In any case, I don’t think that the spec necessarily intended to have the *sender* dereference follower collections for other actors – rather, that role falls on the *receiver*, and the logic/rationale is as described in the Inbox Forwarding section, “avoiding ghost replies”:

> When Activities are received in the [inbox](https://www.w3.org/TR/activitypub/#inbox), the server needs to forward these to recipients that the origin was unable to deliver them to. To do this, the server *MUST* target and [deliver](https://www.w3.org/TR/activitypub/#delivery) to the values of `to`, `cc`, and/or `audience` if and only if all of the following are true:
>
> * This is the first time the server has seen this Activity.
> * The values of `to`, `cc`, and/or `audience` contain a Collection **owned by the server**.
> * The values of `inReplyTo`, `object`, `target` and/or `tag` are objects **owned by the server**. The server *SHOULD* recurse through these values to look for linked objects **owned by the server**, and *SHOULD* set a maximum limit for recursion (ie. the point at which the thread is so deep the recipients followers may not mind if they are no longer getting updates that don’t directly involve the recipient). The server *MUST* **only target the values of `to`, `cc`, and/or `audience` on the original object** being forwarded, and **not pick up any new addressees** whilst recursing through the linked objects (in case these addressees were purposefully amended by or via the client).
>
> The server *MAY* filter its delivery targets according to implementation-specific rules (for example, spam filtering).

Essentially, inbox forwarding is a failsafe that allows a sender to say “look, here’s the Activity, I don’t know what this collection contains, so you deal with it, since it’s ostensibly under your authority.” This is why the “owned by the server” language is used multiple times in the above quoted section. Replying to, tagging, or performing an activity with the (locally owned) actor should trigger some discovery behavior. I must admit I find this recursive discovery behavior a bit problematic, although it is easier to swallow if you take it to mean “remote server implementations may notify / deliver to local actors that you reply to, tag, or act upon”. Perhaps it makes sense to notify someone if they were tagged or replied to, even if they were not explicitly addressed. Perhaps the implication is that they should be careful while doing so to not expand the scope of delivery. Perhaps the language in this third bullet point is simply bad language or bad guidance.

With that said, what matters for the purpose of inbox forwarding is that, if you are delivering to someone else’s followers collection, then the sender needs to know how to refer to the `followers` IRI. But `followers` as a property is only a SHOULD and not a MUST. And I guess the “spam filtering” clause above also opens the door to implementations simply choosing *not* to forward to someone else’s followers. (I think Mastodon does indeed filter out activities delivered to someone else’s followers collection, based on a friend’s testing, but I can’t remember for sure.) Anyway, a server that only implements S2S and not C2S might as well leave out the `followers` and `following` entirely, for privacy purposes. I don’t think this would break anything – if it does, that points to bugs in existing implementations. (Okay, it might break sharedInbox.)

One other tangent about inbox forwarding and addressing in general, that I might follow up on in another topic: I wish implementations would use `audience` more. It should be standard behavior to comment on someone’s post and address them, plus the value of `audience` (plus any other arbitrarily mentioned/added people, plus maybe your own followers). The audience of a post should be used even above `to`/`cc`, because to me `audience` carries human semantics while `to`/`cc`/`bto`/`bcc` are for server communication. For S2S purposes, the server implementation is actually free to deliver several slightly different activities to different inboxes, and in the case of `bto` and `bcc` this differing behavior is actually required. This, combined with the previous paragraph, leads to an issue where one may target their own `followers` collection without actually advertising it as their `followers` collection. I’m not sure what kind of undefined behavior that would lead to… it’s things like this that make me think sharedInbox was a mistake.

---

@macgirvin:

This whole clause is about “conversational delivery” ; which is completely different from microblog use. We use conversational delivery so I understand it very well. This is the two fediverses problem I speak about quite often.

I’ll put it in very simple terms. We have the Twitter fediverse and the Facebook fediverse. The protocol supports the Facebook fediverse through inbox forwarding but Twitter-like projects don’t understand it and do things that break the Facebook fediverse. We can get along together but the first step is acknowledging that both models exist and learning how to implement both. The Facebook fediverse is important for things like private groups (ok “restricted”) and circles (restricted collections), so Twitter-like projects can get some benefit from supporting both.

And we can implement both. In the Facebook fediverse all comments to a restricted (not public) post go back to the sender of the top-level post and to nobody else. The actor that sent the top-level post delivers the comments downstream to all of the original recipients of the top-level post - and to nobody else. If it’s a public conversation commenters can send to whoever they want. This is the secret sauce that makes restricted conversations work.

In this model the sender of the top-level post is the only delivery authority for the entire threaded conversation. Now realise that this section was added to ActivityPub so that Facebook-like projects such as Friendica, Diaspora, and Hubzilla could exist in an ActivityPub fediverse, but the people writing the spec were nearly all from the Twitter side of the fediverse and to this day the Twitter side doesn’t recognise that the Facebook side exists and has a different addressing/delivery model than they do.

It seems from your reply that conversational delivery is a bit alien to you as well. No worries. I’ve proposed a mechanism to let everybody know for any given conversation which model to use. It’s pretty simple and uses ‘replyTo’ and works the same as email’s reply-to. If it’s set, that’s who you reply to - and you don’t cc or deliver to anybody else; and you also copy that replyTo to your own comment before sending. If it isn’t set, send your comment to whoever you want. Twitter-like projects will never set it except maybe in circles/aspects or private/restricted groups. Facebook-like projects will probably always set it except maybe in public conversations when their posts benefit from exponentially widening reach. And everybody lives happily ever after in the same fediverse. We support this today.

---

@trwnh:

Small point of contention: “Twitter fedi” projects like Mastodon implement inbox forwarding, but they don’t make it the main mechanism of delivering replies in the same way that “Facebook fedi” projects expect to redistribute comments.

I am familiar with that model, and in fact Dennis Schubert proposed it some years back in criticism of the “twitter fedi” approach – [ActivityPub - one protocol to rule them all? - Dennis Schubert ](https://overengineer.dev/blog/2018/02/01/activitypub-one-protocol-to-rule-them-all.html#replies-and-interactions) proposed back in 2018 that “Alice’s host is the one who delivered the shareable, so it feels somewhat natural to also ask Alice’s host to distribute the interactions […] a more sensible, reliable, and even more AcitivityStream’y way of handling replies would probably be adding the interaction to the replies collection and sending an `update`”. Whether the original activity is forwarded or whether an Update is sent doesn’t particularly matter to me, since the model works the same either way – your interaction exists in context of some top-level object.

In fact, we could be more explicit with this model by using the `context` property (which sadly goes largely unused on “twitter fedi”). If a post has a `context` of another post, it is a comment (Facebook-style). Otherwise, it is just a post (Twitter-style). These (Twitter-style) posts can exist in context of a Collection representing a conversation, or they can exist in a null context.

> I’ve proposed a mechanism to let everybody know for any given conversation which model to use. It’s pretty simple and uses ‘replyTo’ and works the same as email’s reply-to. If it’s set, that’s who you reply to - and you don’t cc or deliver to anybody else; and you also copy that replyTo to your own comment before sending. If it isn’t set, send your comment to whoever you want.

Hmm, I guess this makes sense as a mechanism – it’s like a more explicit version of `context`. Prior to this conversation I would have probably proposed something like the following algorithm:

1. Try to resolve `context` and check for `attributedTo`.
2. Resolve everything in `attributedTo` and deliver to any `inbox` found.
3. If there was no `context.attributedTo` or no actors/inboxes could be found, or if there was no `context`, then deliver your reply to whomever (your followers, as:Public, etc).

Again, though, I’m not sure if `context.attributedTo.inbox` is the best approach here. At least `replyTo.inbox` saves a lookup step, and allows delivering to different actors, which would allow modeling anonymous objects.

As far as `context` goes, though, it would probably be a good idea to assign `context` = the top-level post (Facebook-style) or `context` = some Collection representing the conversation or thread, and then copy the `context` as-is when replying/commenting.

Perhaps an algorithm which supports both `context` and `replyTo` would look like this in pseudocode:

```
# Determine to/cc addressing
if "replyTo" in activity:
  to = [
    actor["inbox"]
    for actor in activity["replyTo"]
    if "inbox" in actor
  ]

elif (
"context" in activity
and "attributedTo" in activity["context"]
):
  to = [
    actor["inbox"]
    for actor in activity["context"]["attributedTo"]
    if "inbox" in actor
  ]

else:
  to = ACTOR["followers"] + PUBLIC

# Copy any values that should be copied
if "replyTo" in activity:
  replyTo = activity["replyTo"]
if "context" in activity:
  context = activity["context"]
```

---

@macgirvin:

We also use context as you’ve described - but recently a number of new projects have started using context for other things than conversation ids. Fetching our context in fact returns a collection of the entire threaded conversation as seen by the root node - which is something I also wish more projects supported. But now that folks are doing other things with context and we also still have folks using them with OStatus semantics (which aren’t de-reference-able) I suppose we’ll eventually have to come up with a different cross-platform way of fetching the entire conversation. Oh well.

Cheers.

## current thoughts

i plan to synthesize several things here -- a collection that could also be an actor, and maybe set as context. see FEP-7888 and a future FEP for that i guess