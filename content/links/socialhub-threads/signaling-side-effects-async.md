+++
title = "Signaling side effects asynchronously by generalizing Accept/Reject"
date = "2019-10-11"
+++

{{toc}}

## Preserved text

<https://socialhub.activitypub.rocks/t/signaling-side-effects-asynchronously-by-generalizing-accept-reject/125>

<article>

# Background

Per S2S (since Accept/Reject are not described in C2S):

> 7.6 Accept Activity
>
> The side effect of receiving this in an **inbox** is determined by the type of the `object` received, and it is possible to accept types not described in this document (for example, an `Offer` ).
>
> If the `object` of an `Accept` received to an **inbox** is a `Follow` activity previously sent by the receiver, the server *SHOULD* add the `actor` to the receiver’s [Following Collection](https://www.w3.org/TR/activitypub/#following).
>
> 7.7 Reject Activity
>
> The side effect of receiving this in an **inbox** is determined by the type of the `object` received, and it is possible to reject types not described in this document (for example, an `Offer` ).
>
>
>
> If the `object` of a `Reject` received to an **inbox** is a `Follow` activity previously sent by the receiver, this means the recipient did not approve the `Follow` request. The server *MUST NOT* add the `actor` to the receiver’s [Following Collection](https://www.w3.org/TR/activitypub/#following).

Note that for S2S purposes, Accept/Reject is only really defined for Follow activities. Per the Activity Vocabulary, it is also implicitly appropriate for `Offer` and `Invite`, but also [Example 10](https://www.w3.org/TR/activitystreams-vocabulary/#dfn-accept) shows a much more ambiguous Accept Person targeting Group, which seems semantically awkward.

# Possible generalizations

Currently, the majority of the network uses Accept/Reject only for Follow on an Actor, and thus Accept/Reject is used only in that narrow definition. But there are other Activities that might require these semantics…

## Accept/Reject Join

Closed-membership `Group` semantics is maybe the most obvious benefit of generalizing Accept/Reject. It is of course possible to both Follow and Join a Group; Follow Group would semantically translate to being interested in what the Group posts to its outbox, as per the base AP spec. Some softwares handle Group membership based on its `followers`, but this is a bit of a hacky way to do things that is motivated by compatibility with the existing “Follow Actor” network. A much more semantic way to handle things would be to allow Join/Leave Group, but then Join would need Accept/Reject to allow modeling closed groups (at least, in a way different than just using the `Group` `followers` with `manuallyRequiresApproval` + `Announce` any activities received at `inbox` – which is still a valid way of doing things for “mailing list” use cases). This would be clearer than Accepting a Person into a Group.

## Accept/Reject Create

When sending a Create to a remote server, there will generally be side effects interpreted by that remote server’s software. One common example is that sending Create Note with `inReplyTo` may attach your Note as a reply to another Note. But this is not guaranteed to happen, e.g. if the remote software has a concept of “disabled comments” or “approved comments”. In that case, the remote software may want to signal that the use of `inReplyTo` will not automatically cause side effects to be processed, in the same way Follow must first be Accepted or Rejected. It might not make sense to explicitly *require* Accept/Reject semantics for such a case (as they are for Follow), but they can still be used for hinting the result.

## Accept/Reject Update/Add/Remove/Delete

In cases where an actor is performing actions on an object other than creating it, these actions may require approval from another actor who “owns” or “manages” that object. You may want to Accept an Offer of an Update, but it would be simpler to just Accept the Update. Per the AP spec, the only considerations made are as follows:

7.3 Update:

> The receiving server *MUST* take care to be sure that the `Update` is authorized to modify its `object` . At minimum, this may be done by ensuring that the `Update` and its `object` are of same origin.

7.4 Delete:

> (assuming the `object` is owned by the sending actor / server)

7.8 / 7.9, Add/Remove:

> * the `target` is not owned by the receiving server, and thus they can’t update it.
> * the `object` is not allowed to be added/removed to the `target` collection for some other reason, at the receiver’s discretion.

So we have some guidance toward the “receiver’s discretion”, but no formal way of signaling that discretion back to the author of the received activity.

# But why not just use HTTP status codes?

HTTP status codes are synchronously returned at the time of the request, even if the request is handled asynchronously. You may send out a `202 Accepted` to indicate that the document was successfully received, but later send out a `Reject` Activity after some manual action was taken by an actor. This manual action may be performed long after the initial delivery to the remote server.
[/quote]

</article>

---

@nightpool:

I think this is a good idea, but i would like to see some implementor support before defining it further.

---

## current thoughts

yeah, actually. let's do it