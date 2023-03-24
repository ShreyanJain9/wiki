+++
+++

FEP-7888: Demystifying the context property <https://socialhub.activitypub.rocks/t/fep-7888-demystifying-the-context-property/3021>

## conceptual overview

ActivityStreams Vocabulary defines the context property, but it is "intentionally vague". Unfortunately, this makes the definition so vague as to be practically useless. 

Aside from being "intentionally vague", the definition is also somewhat circular; it requires knowing what a context is and having some conceptual understanding of the notion of "context". However, we are given some guidance towards its "intended function", which is to group objects by some common purpose or origin.

We might similarly use a `tag` for grouping objects and activities. Current fediverse projects often include a `Hashtag` (defined as an extension within the ActivityStreams namespace, but not actually adopted or defined formally). This `Hashtag` signals an intent to be included or discovered through a collection of objects bearing the same `Hashtag`, uniquely identified by its `name`. The maintenance of such collections is assumed to be the responsibility of the receiving server, although an `href` is provided for convenience, in order to browse the collection of tagged objects at the source. (This also makes the `Hashtag` a sub-type of `Link`.)

The key property of such a tag is to signal a general, implicit association by reference. We might then consider a context to be an explicit association, but such an explicit association requires an explicit definition.

Various dictionaries define context generally as something that helps you understand the situation. Following from this, the context should be something that helps you process the activity or object. Ignoring the context may lead to misunderstanding the activity or object; the object or activity exists *within* that context.

Specific contexts can be thought of in several applications:

- the "authoritative context" is a context in which some authority can be applied;
- the "conversational context" is a context which represents some conversation and possibly its history;
- the "originating context" is a context which represents some intended starting point that you might look at first.

We might continue to articulate further types of contexts, but the general pattern that emerges is that a context exists to form a purposeful grouping, regardless of the specific purpose. For example, if we had the notion of a conversation, then we might reasonably say that someone owns this conversation and can apply their authority to it. Looking at some object or activity within this context is generally not recommended on its own; it is better to view the entire conversation or some page of it rather than viewing a singular message.

The context may be presented using the following abstractions:

- A "topic" in a forum presentation
- A "conversation" in a social networking presentation
- A "room" in a chatting or messaging presentation
- A "thread" in any of the above contexts (forum thread, social media thread, chat thread)

Contexts may be nested within other contexts:

- A forum topic/thread may be nested in a "forum" or "forum category", and may be nested in another parent forum as a sub-forum.
- A "wall" on a profile or group may contain conversations, which in turn may contain the top-level object and its comments
- A "guild" or "space" may contain multiple chat rooms with a common audience

It is also possible to *not* have a context. Such objects exist only in the general context of their author (via `attributedTo`) and are otherwise self-sufficient. This can include:

- An article published on a web site, particularly one meant to be accessible directly via a permalink
  - A post in a blogging or microblogging environment, particularly one that does not represent a conversation, or where `inReplyTo` is meant only as a loose reference.
- An activity intended for or acting upon an object without a context

## normative language

The use of `context` SHOULD adhere to the following guidelines:

- The `context` SHOULD have a purpose. Consider tags for looser references.
- The `context` SHOULD be resolvable. The resolved object or link should describe the context with at least the additional information needed to fully process the activity or object.
  - The resolved `context` SHOULD be a Collection or a subtype. This Collection SHOULD contain the related items.
  - The `context` MAY require authorization to resolve properly or fully, but SHOULD include an `attributedTo` property at minimum.

When encountering an object with a `context`:

- You MAY copy the `context` as-is, if you wish for your object to be included in the same context.
- You MAY set your own `context`, if you wish for your object to be in a separate context owned by you.
- You MAY remove the `context` entirely, if you wish for your object to exist on its own.

In cases where you copy a context owned by someone else, you SHOULD send your activity to the owner of that context, defined via `context.attributedTo` if resolvable. You SHOULD NOT send your activity to anyone else, unless you implement a mechanism to allow third-party observers to verify that your object or activity is indeed a valid member of the referenced context. (A further FEP may follow up on this topic.)

Upon receipt of such an activity referencing a context owned by you, you SHOULD distribute the object to the audience of the context, specified by `context.audience`, and possibly including `context.followers` if the context is itself an actor. This may be done with inbox forwarding or by delivering an Add activity. An Announce activity SHOULD NOT be used, as this would be interpreted as a reshare. (Verification mechanisms for inbox forwarding or Adding private objects is out-of-scope for this FEP.) You MAY drop certain activities not matching specific policies; for example, you might filter out spam, or implement a policy such that only certain actors (members, participants, etc.) are allowed to be included in the context. (Signaling which actors can participate is out-of-scope for this FEP and may be covered in a further FEP.)

In cases where you copy an unresolvable context, or a context without an owner, you may deliver to an arbitrary audience as if there were no context. However, this is not recommended. Actors that receive such an activity or object with an unverifiable context SHOULD ignore this context, and SHOULD NOT associate the object with the declared context.

### c2s

The following algorithm may be used to create an object within a context collection:

1. Create the `Collection` representing the context. Save the generated Collection `id` to be used in the next step.
2. Create the `Object` and specify the `context` as the `id` obtained in step 1. Set an appropriate `audience` or use `to`/`cc` to deliver the Create activity as-is. Save the returned Object or its `id` to be used in the next step.
3. Add the `Object` to the context `Collection`, using the response from step 2. You may wish to deliver this Add activity `to`/`cc` your intended recipients, especially if you did not deliver the Create Object from step 2.


## future directions: contexts as actors that can be followed and addressed