+++
+++

# the semantics of some types are unclear or poorly defined

## Note vs Article

see [Note vs Article]({{<relref "note-vs-article.md" >}}) for more. but basically:

in summary:

> Note: Represents a short written work typically less than a single paragraph in length.

> Article: represents any kind of multi-paragraph written work.

but this is contradicted by as2/ap examples.

the messy general consensus post-spec seems to be about "intention", but this is never defined. it is unclear what a `Note` is intended to be, and what an `Article` is intended to be, beyond very loosely and very vaguely tying such notions to app-specific abstractions or behavior. for example, Mastodon will show a Note more or less in full, but convert an Article to a title and url.

it would have been better to define it similarly to HTML `<article>` perhaps? an independent unit of writing? one that is published and therefore might reasonably be syndicated?

## Mention

> Mention: A specialized Link that represents an @mention.

this is an incredibly narrow definition and also one that is often useless. what's so special about an @mention?

it would have been better to define it in terms of, idk, generating a notification or something? like a webmention?