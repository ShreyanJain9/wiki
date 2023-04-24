+++
+++

# Move

used for account migrations

## sample payload

### mastodon

<https://github.com/mastodon/mastodon/blob/main/app/serializers/activitypub/move_serializer.rb>

```json
{
	"@context": "https://www.w3.org/ns/activitystreams",
	"id": "https://mastodon.example/users/username#moves/1",
	"actor": "https://mastodon.example/users/username",
	"type": "Move",
	"object": "https://mastodon.example/users/username",
	"target": "https://social.example/users/new_user"
}
```

