people think activitypub is a network protocol when it's really more like a programming language. you don't say "compatible with c++" you say "written in/with c++". similarly, we might say that fedi is "written with activitypub" or "implemented using activitypub", not "compatible with activitypub" or anything suggesting a network.

analogously:

ap = inter-process communication (e.g. d-bus)
as2 = data interchange format (e.g. protobuf)
json-ld = media type
rdf = facts and logic

if we're being honest the majority of "fedi" is part of the "network" that is "compatible with mastodon". mastodon *is* the network protocol. we just express it in terms of "mastodon-flavoured activitypub"

put another way, there is no such thing as "fedi", but rather, multiple different and distinct "fedis" that only partially overlap.

it occurs to me that activitypub may be best thought of as a cms with an optional distributed storage backend

similarly: no one in fedi is doing real activitypub. it is probably more appropriate to say we are doing AS2 + Linked Data Notifications

side effects are more accurately defined by the mastodon documentation than by the activitypub spec

we're kind of in the situation where someone has invented the C Programming Language, maybe D-Bus, but we still don't have a FreeDesktop.org or XDG equivalent.