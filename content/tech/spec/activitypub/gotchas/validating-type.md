## DO NOT CHECK TYPES DURING VALIDATION

an Actor has an `inbox` and `outbox`. that's it.

an Activity has an `actor`. that's it.

a Collection has `items` or `orderedItems`. that's it.

etc

## DON'T PANIC WHEN YOU SEE A TYPE YOU DON'T UNDERSTAND

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