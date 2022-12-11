# Gotchas

## @context

** DO NOT VALIDATE @CONTEXT IF YOU DO NOT UNDERSTAND JSON-LD **

you CANNOT check for the presence of `https://www.w3.org/ns/activitystreams` AT ALL -- the activitystreams context may be included within another context hosted elsewhere. just ignore this property entirely if you don't understand it

## id

### transient ids

do NOT try to be overly strict about dereferencing IDs. some IDs may not be on your domain, or otherwise they may not be present at all, or they may be explicitly null.

#### example: Follow semantics

Follows are realistically transient. it is therefore enough to:

- keep track of local state
- mutate state based on activities

if you receive an Accept/Reject Follow, check ONLY for the following:

- actor
- type == Accept/Reject
- object.actor
- object.type == Follow
- object.object == actor

this is enough information, PROVIDED THAT you have a local pending follow request. if you do not have a pending follow, then DO NOT process an incoming Accept Follow. however, you may receive a Reject Follow at any time, indicating that you should destroy that follow relationship. note that you may also receive an Undo Accept Follow by some implementations. this is discouraged but should be handled as well

## type

### Actor

has an `inbox` and `outbox`. that's it.

### Activity

has an `actor`. that's it.

## tag

### filter for what you understand

say you understand tags of type Mention and Hashtag and Emoji. someone sends you a `tag` array with a raw Link. DON'T PANIC. the document is still valid. just filter out anything you don't understand, something like

```python
UNDERSTOOD_TAG_TYPES = set("Mention", "Hashtag", "Emoji")
document = json.loads(...)
tags = document.get('tag', [])
tags = [
	tag
	for tag in tags
	if tag['type'] in UNDERSTOOD_TAG_TYPES
]
# do whatever you need to now
```

```javascript
const UNDERSTOOD_TAG_TYPES = new Set(["Mention", "Hashtag", "Emoji"])
let document = ...
tags = document.tag.filter(tag => tag.type in UNDERSTOOD_TAG_TYPES)
// do whatever you need to now
```