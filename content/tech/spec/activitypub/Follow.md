+++
+++

## what Follow is ideally like in theory

- you send a Follow
- they send you an Accept Follow as a courtesy, and Add you to their `followers`
- you receive the Accept Follow and take it as a signal to Add them to your `following` (perhaps you can even show the Accept Follow as proof of this, if it was signed!)

## what Follow is actually like in practice

sharedInbox exists, and known follow relationships are used to calculate visibility on the receiving end (instead of relying on direct delivery). so now that Accept Follow actually matters... it's not just a courtesy anymore.

because of this, Follows are realistically transient requests to update state. it is therefore enough to keep track of local state, then mutate state based on activities.

if you receive an Accept/Reject Follow, check ONLY for the following:

- actor
- type == Accept/Reject
- object.actor == (you)
- object.type == Follow
- object.object == actor

if object is inlined, you don't need to check that object.id is local. the above is enough information, PROVIDED THAT you have a local pending follow request. if you do not have a pending follow, then DO NOT process an incoming Accept Follow. however, you may receive a Reject Follow at any time, indicating that you should destroy that follow relationship. note that you may also receive an Undo Accept Follow by some implementations. this is discouraged but should be handled as well

<https://github.com/misskey-dev/misskey/issues/9250>

if you receive a Follow from someone you already Accepted, then send another Accept Follow to remind them. in practice, this usually means a state desync or database loss.