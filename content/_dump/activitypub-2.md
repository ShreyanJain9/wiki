https://mastodon.social/@trwnh/109979200684979970

@ people who keep saying we should rewrite the activitypub spec to ensure better interoperability with existing fedi projects

[](specs-are-enough.jpg)

idk how else to say that if you want to be compatible with a project then you need to have a shared worldview and conceptual space, and this necessarily involves communicating with that project on some level (by reading their documentation, asking their devs questions, etc) in order to know what their expectations and requirements are

the question of "interoperability" makes no sense on its own. you must always ask, interoperability with what? and on which conditions?

like, we all use activitypub more or less according to the spec, and that's because the spec is simple, it's basically just "HTTP POST to inbox". we could theoretically construct any as2 payload we wanted, but we largely limit ourselves to Create Note... why? because you want to be compatible with mastodon, right? and that's the real issue.

there are things that mastodon et al do that are within some spec -- webfinger for translating usernames to actors, for example. then there are things that are technically a spec but outdated, like http signatures (cavage draft) and ld signatures (obsoleted entirely). and then there are things that are not specced -- quirks of the implementation that must be respected, things like using Note, how to structure a Mention, the use of specific algorithms for signatures. maybe these can be negotiated

crucially, sometimes the assumptions you make, the quirks your software has, these will not line up with the assumptions that others make, the quirks that their software has. you might even disagree entirely, and without the possibility of reconciling those opposing assumptions.

consider the assumption that every actor has a username, and that this username identifies them more canonically than their own activitypub identifier. what if you disagree? well, that's an incompatibility.

how do you handle the root of identity being the activitypub id, versus the root of identity being the username, versus the root of identity being some cryptographic key? what is one to do, if one wants to be "compatible" and "interoperate" with three different softwares each following a different one of the above assumptions about identity? are these softwares even meaningfully part of the same network? and which of these three networks is the "true" "fediverse"?

and even within those identity schemes, we might still have to consider further parameters, like the expectation of only supporting HTTPS URIs. or, let's imagine that we choose to use or support DIDs -- now, which DID methods do we support?

all of these are decisions that each project has to either make for itself, or vest authority in some other entity to make those decisions in a binding way -- by publishing some meta-spec, some conformance profile, basically a bundle of specs that should be followed and their particulars.

and even *then*, you will find some quirks remain, some projects are coded in a way which has implicit expectations. the more you try to eliminate these, the narrower the scope or conceptual space becomes.

with enough limitations, you end up with what is basically the same software, just with different implementations. if mastodon, pleroma, and misskey all were overspecced like this, you may as well view them as "fedi implemented in rails", "fedi implemented in elixir", and "fedi implemented in nodejs".

so, in closing, i leave with this to consider:

is your view of the "fediverse" limited only to the conceptual space of existing projects? and, if so, then which ones?

or will you allow yourself to dream a little bigger?

https://www.youtube.com/watch?v=WcGbnX8Ay38