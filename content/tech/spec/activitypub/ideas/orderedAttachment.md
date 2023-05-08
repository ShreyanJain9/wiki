problem: sometimes you want to have attachments in a certain order, but `attachment` is an unordered set by default

solution: define `orderedAttachment` similar to `orderedItems`

```json
{
	"@context": [
		{
			"orderedAttachment": {
				"@id": "https://...",
				"@type": "@id",
				"@container": "@list"
			}
		},
		"https://www.w3.org/ns/activitystreams"
	]
}
```

you could maybe do the same for `orderedTag` but idk if that makes full sense bc `tag` is not really used for categorization so much... but if tumblr or a tumblr-like implementation wanted to order their tags then they have no way of doing so