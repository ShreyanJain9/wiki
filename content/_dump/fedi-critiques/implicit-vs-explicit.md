the way to maintain an authoritative collection is to send out Add/Remove, not to have everyone send out Create and then expect everyone else to reconstruct the collection for themselves

i can't say i blame developers for taking the lazy route and doing the bare minimum to be compatible with what already exists, but that is so limiting and i'm not here for it

the bigger problem is that there's so many assumptions being made with zero basis or foundation in anything. we're just replicating stuff around and then every server assembles it however it *thinks* it should be put together.

we're getting to the point that the contradictions and shortcomings are starting to become more apparent...

- things like moderated conversations are impossible if you don't actually have a concept of a "conversation".

- there's so many different ways to do "groups" that it's a running joke at this point, because everyone has a different idea of what a "group" is. it's the "blind men feeling an elephant" problem.

- things like forums and subforums, chat rooms, etc are not easily possible rn.

the "trick" is that there isn't actually any difference between all these different presentations. if you understand "objects" and "collections" you can construct whatever you want: forums, walls, chat rooms, conversations, aggregators, whatever. it's all just objects in collections.

right now, no one understands "collections". we just pass around objects with Create and Announce and leave it up to the consumer to figure out how they should be grouped.

it's not even a new idea. Dennis Schubert raised a similar criticism of "replies" distribution back in 2018: https://overengineer.dev/blog/2018/02/01/activitypub-one-protocol-to-rule-them-all.html#replies-and-interactions

> As per the ActivityStreams spec, all objects [can] have a replies property. So, a more sensible, reliable, and even more AcitivityStream’y way of handling replies would probably be adding the interaction to the replies collection

this is not always optimal of course -- it was proposed 5 years ago. but something similar could be done.

i think the key disagreement, misunderstanding, lack of shared reality, etc, is this:

are we just creating a bunch of "posts"? microblogging or regular blogging, we have our own websites or web profiles, and we only care about what we ourselves are sharing?

or are we trying to collaborate across domains? are we building a Web, a Social Web, a Linked Data Web, a Semantic Web? where our actions have side effects, and there exists some state that we care about?

we lack that clarity of purpose

basically we're not doing one or the other, we're kind of mishmashing them together. when you post on most fedi projects, you are publishing to your profile locally, *but* you are also sending out an activity to notify your followers. and typically, that has side-effects on remote servers. usually that side-effect is "keep a locally cached archive of that post and show it to followers."

i think we should be clearer about the separate use-cases and concerns.