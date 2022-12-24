+++
updated = "2021"
+++
## foundational concepts
- regular grammars, subject-verb-object, and the actor system
- addressing and identity
- centralization, authority, and the open-world system
- fetching vs delivery, and dealing with state
- authentication and authorization

## real life and prior art
- physical building/mailing addresses and email
- phone numbers and sms
- the many im apps, attempted unification, and current fragmentation
- spec talk: smtp, irc, xmpp, activitypub, matrix, proprietary ReST APIs, and so on

## introduction to activitypub
- activitystreams vocabulary
- web uri
- as2 serialization and json-ld
- inbox and outbox GET and POST
- cryptography, signatures, tokens for validation and access control

## let's design a social network
- what's wrong with what we have now?
- what we can do better
- types of communication: 1-1 (chat or publish), closed/open group (room or wall)

## my ideas
- mapping resources by uri, not just by https url, but also possibly by a urn resolving service?
- publish only what the user consents to. assume everything is private by default. including uris, in some cases.
- contact manager to map addresses to *people*. keep track of not only who has which address, but also *when* they had the address (in case the address changes!)
  - id assignment should be on its own namespace and you should leave human-readable stuff for the url. url should resolve to id. 
- Join a Group (for arbitrary delivery ingroup or serverside validation) vs Follow a Group (for its public postings, both Announce from group members but also directly authored by the Group actor itself)
  - Follow a Service that will send you messages like a mailing list?
  - maybe delivery-by-default isn't the best model for everything? maybe all you really need is cross-domain auth?
  - n-way delivery is a nightmare anyway. better to deliver to Service then it redistributes to all participants (can use "context" to model rooms). this works like irc but with federated id and richer as2 vocab
- open registrations are a mistake. you are responsible for everyone that you provide service to, and every page published on your website
- Accept or Reject more things. let em know when side effects were processed. or use a signed Accept token during validation. or simply have both actors sign off on it.

### other ideas

- no-cost push messages = spam. being able to send you stuff is not something to be taken lightly. generally on the web, you pull stuff you're interested in (sub) that other people have made available (pub). assuming that everyone should be able to push stuff into your inbox is not a good idea. ultimate control for what you receive should be given to you, the recipient -- you should be able to refuse delivery of anything you don't want. with that said... push should be a permission or *capability* that you can grant to others, if you *trust* them.

- related to push, is the ceding of the replies/comments section as out of your ultimate control. twitter for example fucked up when they made @ mentions deliver directly to someone. imo it would have been better to only see @mentions when you explicitly go searching for them (like hashtags). 

- another thing twitter did: full-text search. oh god it sucks so much when harassment mobs search for a specific term and then descend on anyone who uses it. rethink the boundaries of what should be considered "public" or "searchable".

- assuming public by default. no. let the user determine what they want to *opt in* to publishing, not what they want to *opt out* of having visible. by default, nothing should be known about the user.

>Copying and pasting made people look at what they shared, and think about it, at least for a moment. When the retweet button debuted, that friction diminished. Impulse superseded the at-least-minimal degree of thoughtfulness once baked into sharing. Before the retweet, Twitter was largely a convivial place. After, all hell broke loose — and spread.

--[The Man Who Built The Retweet: “We Handed A Loaded Weapon To 4-Year-Olds”](https://www.buzzfeednews.com/article/alexkantrowitz/how-the-retweet-ruined-the-internet)

>[S]ocial media has been overtaken by metrics, which are driven in large part by the vicious cycle of advertisers wanting to know which influencers are worth paying; and by toxic fan battles to make your favorite social media accounts gain followers and likes, and to downrank your favorites' rivals. 

--[Demetrification: improving social media by removing public like/follower/repost counts](https://boingboing.net/2019/09/12/flickrs-glory-days.html)