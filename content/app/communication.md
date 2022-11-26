# Communication paradigms

## qualitative analysis
- deliver / fetch
- side effects (receive or relay?)
- 1-1, 1-n, n-n?

## types of communication
### Chat (n-n delivery)
- all kinds of messengers
- fixed participants/addressing
- smtp, xmpp, activitypub
### Broadcast (1-n delivery/fetch)
- feeds/social
- make a resource available to audience
- actitypub (when not @ anyone), rss, atom
- xmpp status updates / PEP, "stories" (loosely)
### Wall/Forum (1-n relay/fetch)
- Facebook profile/group page, bulletin board forums
- loosely organized by page
- activitypub only? which vocab to use? what type of actor? how to follow a thread? needs extensions...
### Room (n-n relay)
- IRC and its various clones, mailing lists, etc
- irc channel, smtp mailing list, xmpp muc, activitypub Service actor?

## What do i actually use tho

- read (feeds -- rss, atom, activitypub?)
- publish (broadcast -- but also websites/pages/etc?)
- chat (chat -- messaging over smtp/xmpp/activitypub)
  - 1:1 chat
  - closed group (define participants)
    - deliver to all those participants, or have them fetch it from a central Service? (probably the former)
  - open group (participants can join/leave at any time without approval)
    - better served by room model than chat model
    - delivery to all participants becomes impractical and limited, so central Service becomes more necessary to store and forward
- lurking in rooms?
  - kind of like read, but with ability to send (so a mix of read + open group?)
  - rooms are actually misused imo! people turn irc channels into the equivalent of forums and it's actually the wrong paradigm entirely
    - i think rooms should be created and destroyed as needed for when people need to actually have a live chat
      - i'm mostly fine with offloading this to jitsi i guess? no pressing reason to implement my own stuff for this, and also jitsi provides voice/video chat and screensharing at the same time so it's probably way better than anything i could ever do on my own
      - perhaps by design rooms should ask the user to turn on logging? to maybe communicate that this is not a permanent place for discussion? or an option to destroy the room and convert the chatlog to a forum post? idk...