+++
title = "Resolving the Note vs. Article distinction"
date = "2019-11-01"
+++

## preserved text

<article>

# Background

[Activity Vocabulary - 3.3 Object Types ](https://www.w3.org/TR/activitystreams-vocabulary/#object-types):

> **Note**: Represents a short written work typically less than a single paragraph in length.
> **Article**: represents any kind of multi-paragraph written work.

Example 48 (Article):

```
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "type": "Article",
  "name": "What a Crazy Day I Had",
  "content": "<div>... you will never believe ...</div>",
  "attributedTo": "http://sally.example.org"
}
```

Example 53 (Note):

```
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "type": "Note",
  "name": "A Word of Warning",
  "content": "Looks like it is going to rain today. Bring an umbrella!"
}
```

Semantically, the difference is never explicitly defined (how do you define a “paragraph”?), so the current fediverse has sort of assumed Article should be viewed natively on the remote website, while Note can be displayed as an inline status. Thus, Note is used to represent a status update, and a lot of the network just defaults to Note. The distinction is assumed to be formatting, but once again this is not an explicit definition (how do you define “formatting”?)

# Disambiguation

Going purely from the Activity Vocabulary descriptions and examples, I would possibly assume one or both of the following:

* Note SHOULD be plain text, Article SHOULD use HTML (or should these be a MUST?)
* Note SHOULD NOT use newlines (but are technically allowed to do so)

However, there is [ActivityPub 3.3 ](https://www.w3.org/TR/activitypub/#source-property), Example 8:

```
{
  "@context": ["https://www.w3.org/ns/activitystreams",
               {"@language": "en"}],
  "type": "Note",
  "id": "http://postparty.example/p/2415",
  "content": "<p>I <em>really</em> like strawberries!</p>",
  "source": {
    "content": "I *really* like strawberries!",
    "mediaType": "text/markdown"}
}
```

This example Note uses HTML for its `content`, in order to demonstrate the `source` property.

Also, ActivityPub Example 4:

```
{"@context": "https://www.w3.org/ns/activitystreams",
 "type": "Create",
 "id": "https://chatty.example/ben/p/51086",
 "to": ["https://social.example/alyssa/"],
 "actor": "https://chatty.example/ben/",
 "object": {"type": "Note",
            "id": "https://chatty.example/ben/p/51085",
            "attributedTo": "https://chatty.example/ben/",
            "to": ["https://social.example/alyssa/"],
            "inReplyTo": "https://social.example/alyssa/posts/49e2d03d-b53a-4c4c-a95c-94a6abf45a19",
            "content": "<p>Argh, yeah, sorry, I'll get it back to you tomorrow.</p>
                        <p>I was reviewing the section on register machines,
                           since it's been a while since I wrote one.</p>"}}
```

This example Note uses two `<p>` elements, representing two short paragraphs (once again not “less than a single paragraph”).

So even the specs themselves are inconsistent on any distinction.

# How much does this actually matter?

Arguably not much, since implementations often convert Note and Article into their own internal schema for statuses anyway. But it could still be beneficial to set a clearer distinction going forward on how these types should be assigned, ideally.

</article>

---

@lanodan:

The distinction I make between Article and Note isn’t related directly to it’s content but on how it’s supposed to be presented and used, Articles are more things for blogs where you have about a post per day and so articles should be easy to find back with a list of articles/tags and maybe a bit of search features, Notes are more stuff like microblogging where you can easily have hundreds in a day and aren’t that easy to find back even with good keywords in full text search.

Also I find that formatting is actually very useful for notes because it allows to express more/equivalent with less (like a word-list vs a paragraph).

This question goes a lot in the fediverse because they are the two mainly used activity types but one could also ask about the actor distinction between Organisation and Group, Application and Service. And so far I’ve only seen ActivityPub Document be used in the wild for Images with textual description (like if Image couldn’t have it in the first place), but Document has no inherent meaning either.

Note: Pleroma keeps the distinction between Articles and Notes internally, no real differencies for the Mastodon API though but there could be a query filter.

---

@trwnh:

For prior art, I can think of semantic HTML’s `<article>` being a section of HTML that can be reproduced elsewhere in its entirety.

W3Schools: "independent, self-contained content. An article should make sense on its own and it should be possible to distribute it independently from the rest of the site."

MDN: "a self-contained composition in a document, page, application, or site, which is intended to be independently distributable or reusable e.g. in syndication."

This could be a distinction worth making, maybe? that an Article should roughly map to an `<article>`, whereas a Note is just arbitrary text?

---

@nightpool:

I think using `Article` is a perfectly fine solution for when the desired semantics are “I think it’s better in a short-form microblogging context to display a link to this thing rather then the full content itself”.

When considering that some clients (especially native clients) often won’t support increasingly rich text formatting, I think using Article vs Note for content where you think plain-text fallback is unacceptable is a totally fine decision to make. (But you shouldn’t ignore the fact that it’s probably likely that people are going to write long-form clients that *do* support Articles but don’t support every possible type of rich text formatting, like the way RSS readers restrict allowed formatting for readability)

---

@trwnh:

I can’t remember where this was said to me, but I think someone (maybe nightpool?) suggested that Note could be used for message passing a la Discord, which would be a third distinction and perhaps better than length/newlines or plaintext (as explained previously why those are inconsistent). So in that case, we could say Note vs. Article is one of *formality*. IMO this could complement the Article-to-`<article>` suggestion above.

Taken together:

* Note = for informal message-passing
* Article = for publication or syndication

The only thing unresolved with this distinction would be the blurry line that is microblogging. Microblogs are often both informal *and* intended for republishing in feeds. It’d be pretty clear-cut to use Note for a messaging application and Article for a blogging platform, but… perhaps this implies top-level microblogging posts should be an `Article` and replies should be `Note`? Or that they should all be `Article` regardless of the fact that they are informal? There’s still room for discussion there.

---

@darius:

I think this is right on the money. My main use case for Article is that it’s a way to say that “this is a publication” – I’ve often talked about how this might actually help solve the “quote-tweet” design problem in Mastodon (and presumably other software). If an Article is something that is formal and published then it’s also something that can reasonably be commented upon. Articles can be quoted-commented, and Notes cannot. So I can dunk on a New York Times article, but not on a random thing someone posted as a note.

Edited to add: for composing posts on a microblogging service, I wonder if a “people can quote-comment on this post” checkbox or whatnot could be, at its most basic, a switch that underneath it all changes type for your post from Note to Article.

---

@trwnh:

quoting and commenting is kind of out-of-scope and it’s up to each platform to decide how they want to handle it, tbh. from a data view, it doesn’t really matter because you can put whatever you want in `content`, and `inReplyTo` is just metadata (like `tag`). You can have an Article inReplyTo another Article, just as you can have a Note inReplyTo another Note (or a Note inReplyTo an Article, or even vice-versa, etc etc.)

the thing is, though, that it’s really seeming like if we use “syndication” as the distinction, then that would imply a lot of things that are currently Note might be better conceived of as Article.

---

## current thoughts

i wrote a whole thing abt this [here]({{< relref "note-vs-article" >}})