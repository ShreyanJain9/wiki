## DON'T BE STRICT ABOUT VALIDATING ID

do NOT try to be overly strict about dereferencing IDs. some IDs may not be on your domain, or otherwise they may not be present at all, or they may be explicitly null. null or missing id indicates a transient activity.

### example: Follow semantics

Follows are realistically transient. it is therefore enough to:

- keep track of local state
- mutate state based on activities

if you receive an Accept/Reject Follow, check ONLY for the following:

- actor
- type == Accept/Reject
- object.actor
- object.type == Follow
- object.object == actor

if object is inlined, you don't need to check that object.id is local. the above is enough information, PROVIDED THAT you have a local pending follow request. if you do not have a pending follow, then DO NOT process an incoming Accept Follow. however, you may receive a Reject Follow at any time, indicating that you should destroy that follow relationship. note that you may also receive an Undo Accept Follow by some implementations. this is discouraged but should be handled as well

<https://github.com/misskey-dev/misskey/issues/9250>