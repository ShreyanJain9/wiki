+++
+++

# Note vs Article

Discussion: <https://socialhub.activitypub.rocks/t/resolving-the-note-vs-article-distinction/258>

in summary:

> Note: Represents a short written work typically less than a single paragraph in length.

> Article: represents any kind of multi-paragraph written work.

but this is contradicted by as2/ap examples

## the practical difference

a Note will be displayed inline in Mastodon, but an Article will be converted to name/summary + url/id

further recommendations for microblog compatibility:

- a Note SHOULD still work with a plaintext fallback, as most html could be stripped
- a Note SHOULD NOT have a name/title, as this will be ignored

if you don't care about Mastodon or similar microblogging impls then do whatever. some guidelines/thoughts below...

## failed ideas for distinction

### a "paragraph"

perhaps a note could have no line breaks or only a single `<p>` tag, while an article does? well there's an example that directly contradicts this! ActivityPub example 4 uses two `<p>` tags inside a Note's content

### titles

some say a Note has no title but an Article does -- this is not strictly true in all cases, a Note can have a `name` (and AS2-Vocab example 43 does so)

### formatting

some say a Note has no formatting but an Article does -- this is not true at all, as Note can be HTML and is in fact assumed to be HTML by default (per AS2-Vocab definition of `mediaType`)

### length

perhaps a Note has some character limit and an Article doesn't -- but what is the limit? completely arbitrary. not worth making this distinction

## what's left for distinction?

### syndication

an Article might be expected to be syndicated (think HTML `<article>`) since it is a self-contained piece of content. whereas a Note might not be

<https://html.spec.whatwg.org/multipage/sections.html#the-article-element>

> The article element represents a complete, or self-contained, composition in a document, page, application, or site and that is, in principle, independently distributable or reusable, e.g. in syndication. This could be a forum post, a magazine or newspaper article, a blog entry, a user-submitted comment, an interactive widget or gadget, or any other independent item of content.
>
> Note: When used specifically with content to be redistributed in syndication, the article element is similar in purpose to the entry element in Atom.

however this may imply that microblog posts should be sent out as Article? requires further thought

### formality

an Article is "published" in a formal context whereas a Note is presented in an informal context

definitions for "article often stress that it is an independent unit of writing, and is often published.

dictionary.com:

> a written composition [...] forming an independent part of a [...] publication

merriam-webster:

> a distinct section of writing [...] often numbered

> [a] composition [...] usually forming an independent part of a publication

in other words a Note may be viewed in passing, while an Article is more persistent

## indieweb perspective

indieweb seems to take this view <https://indieweb.org/Semantics_Of_Article-Note_Distinction>

> post name/title [though discounted above]

> non-trivial structure [akin to formality -- headings?]

> plain text vs markup [basically the p-content vs e-content argument]

## takeaway thoughts

i would tend to assume everything is an article by default, with certain characteristics making it tend toward being a note instead

for content:

- no headings?
- no formatting?

for metadata:

- no `name`?
- no `id` (transient or anonymous objects)?

but it's really that last one that seems most salient to me. consider an Article to be a top-level text container that can be referred to later since it forms an independent published unit of writing. consider a Note to be an anonymous text container to be embedded in other objects. in practical terms, a Note might be used for actor metadata, in which case it would have a `name` but still be a Note

```yaml
type: Note
name: "Pronouns"
content: "they/them"
```

note that Note might still have an id, though... so really it's just a vague ill-defined "intention". for example, maybe use a Note for a "status update" in a chat/presence sense? like XMPP PEP.