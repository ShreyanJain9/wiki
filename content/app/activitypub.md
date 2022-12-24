+++
+++

# ActivityPub

## HTTP verbs
GET and POST/PUT/DELETE[^1] json docs? maybe some validation? needs some basic authentication too i guess

~~uri schema is handled by the client, not the server (client PUT).~~ activitypub section 6 says client should POST either an activity or an object to /outbox and then the server returns the `id` value. Server decides id, client does NOT get to decide what id is. it only gets to decide `url`.

## authorization

maybe need a token or capability to obtain read/write privs to given directories or objects? need to look into how to generate and revoke captokens and how to expose that info to clients through a standardized endpoint... OAuth? an admin token could manage and revoke which tokens have access to read/write to certain resources, maybe? but there would have to be a way of linking tokens to people or device names somehow for it to be human-meaningful...

the real issue is that i need to figure out how to create and manage actors. we can say that the oauth flow can request scopes as paths e.g. `"scope": [{"/": "rw"}]` for full access to all paths (or something like that)... but ugh idk. maybe be lazy and fallback to POSIX filesystem ACL? what that indicates is that the server will have to do its own access control but idk how. i need to figure out how.

## how to create and manage actors?
xmpp i would do like `prosodyctl adduser JID` and `prosodyctl passwd JID` so you could have a cli tool to generate read-write tokens maybe? idk. that kind of out-of-band token generation seems a bit complicated but it might be necessary if we don't want usernames to be canonical. maybe some config file could work? really lost here...

## spitballing about c2s

if the defined actor flow is as follows:

1. ask for actor
1. discover outbox endpoint
1. POST to outbox
1. server will assign id and return it to client with 201 + Location header

then idk what that means for my plans with id/url distinction... i think it means that clients have to save the id internally if they want to refer to objects later, for example. if a client POSTs an Object and it gets wrapped with a Create and then both ids are assigned... did the client get to decide the URL? and how does the server know if the URL is allowed? presumably then, we have two different levels of authorization to deal with -- one for the actor namespace (but this can be handled by the server tbh), and then another for where the client can serve? or... maybe this means that the client and server ideally should be running on different sub/domains as i suspected! **if you put the server and client on the same FQDN then there will be URL/namespace conflicts.** the server will create e.g. /actors/1/outbox and generate a corresponding OAuth token maybe. the server probably also needs to know which namespaces it actually has access to? some way to map a more human-friendly identifier to the activitypub id? can we mandate webfinger?

## footnotes
[^1] POST means the server decides the URI of the resource it receives, PUT means the client decides and the server just dumps it there