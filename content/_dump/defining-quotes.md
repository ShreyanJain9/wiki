+++
date = 2023-05-02
+++

fwiw my take about quotes is that either

- they're just links with special tag markup that lets them be identifiable as activitypub objects
- they're supported more at the protocol level a la Announce with content, or possibly a "quotes" collection a la "likes" and "shares"
- they represent a new context (assuming mastodon ever adopts a first-class concept of conversations or threads, instead of relying solely on inReplyTo chains for logical grouping)

my further idea was to decide if it notifies based on whether the type is Link or Mention (a la webmentions)

in an alternate timeline we could have had generic rendering of activities based on name/summary/content and maybe actor/type/object

such a generic renderer would be something like...

> <darius> <Announce> <some-post>
> <Announce.summary>
> <Announce.content>

filling in the last two lines for summary and content

> Darius boosted a post: "The problem with this is..."
> "I disagree with this analysis."

also this just highlights what i think a "quote post" should really be: more like a "loud reply". in a more ideal world it would use `inReplyTo` + a new `context`.

yes, a regular "reply" is in effect a "quote" the second you embed the post you are replying to. look at the IndieWeb for example. what is the functional difference?

the problem of course is that mastodon doesnt show replies in timelines, and doesn't have a proper concept of contexts aside from reply-chains. but at least half of that is a matter of policy, so it could be changed.

imagine a property `toot:quoteReply` that is a boolean. if true, it embeds the `inReplyTo`post above your post. it also sends such posts to follower timelines.

fundamentally there's the intent and the interpretation, intent can be seen as how you author the activity, interpretation is the processing rules applied by the receiver. if this were a proper spec, the two would be the same and there'd be no ambiguity, but... well, y'know.

there's also the rest of the ecosystem of course, there's the "tag a substring of your content" as Foundkey does, in the past there was even "attach the post you're quoting" which Hubzilla and/or Zap did, there was also the proposal to "add content on Announce" which gargron and lain favored but never implemented. and then there's the two new ones -- "just make it a special type of reply" as i propose, and "just make it a new Activity" as you expressed just now

we should focus on generic data modeling, describing resources and how they are related. mastodon doesn't do a good job of that, sure... and that's how we end up with leaky abstractions. it's a good idea to leave as much of the app-specific stuff out of the data model as we can. otherwise we just end up with The Mastodon Protocol which is built to interop with the mastodon app specifically

like we have tag, attachment, inReplyTo, Announce...

- tag = describing entities within the natural language properties (often microsyntaxes)

- attachment = sidecar entities (not necessarily media)

- inReplyTo = referencing a prior object (or objects) for which your current object can be considered a response

- Announce = add to `shares` collection if present (this is the weakest one, to be fair)

> I do agree with you that quotes are basically a form of loud reply

semantics aside, i think functionally the difference is really the fallback

- an Announce with content is basically "reshare with additional content" semantically, but it falls back to being a regular boost, and to control quotes you'd need to similarly be able to control boosts

- a Note with inReplyTo + quoteReply=true would fall back to a regular reply. this means that when reply controls get added, you get quote control "for free"

the other functional difference between those two is whether the "quote" gets added to `shares` or to `replies`

---

also tangentially i've thought about what people think a quote is vs what it actually is, the dimensions, its primary function, etc

to me, the way i see quotes is essentially as a "loud reply" or as a "breakout thread".

the former use-case could be handled by reply+boost. this is actually very easy in the API: just make a button that fires off 2 API calls.

the latter could be handled by copypasting a link into a new post. this is much harder to detect and display. optinally mention

really the problem with twitter "quote tweets" is that they conflated the two use-cases

you were *always* notified, regardless of whether someone was talking "about" you or "to" you (no consideration for replies)

plus the fact it was a new thread was stripping context, you could no longer see all the posts in one thread ("breaking out" isn't always wanted)