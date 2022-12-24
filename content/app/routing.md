+++
+++

# Routing table

## trwnh.com
- /.well-known/webfinger -> ???
  - should map a@trwnh.com to ap.trwnh.com/users/ea9f49f6-d1cc-4b9e-a221-71f6aa617efa
- /notes, /articles, etc etc -> broadcast.trwnh.com [maybe? or do i just want to run the c2s app on the root domain? indieweb? idk]
  
## ap.trwnh.com
- /users
  - /actors? how do i want to separate profiles from users? users should be able to have multiple profiles.
- /inboxes
- /outboxes
- i have absolutely no idea how to handle authorization beyond vaguely something oauth-ish
  
## chat.trwnh.com
smtp / xmpp (jsxc embedded?) / activitypub dms

## rooms.trwnh.com
irc, xmpp groups, i think it makes sense to add webrtc too. basically jitsi meet or nextcloud talk, but with persistence. 

## reader.trwnh.com
rss / atom / activitypub?

##