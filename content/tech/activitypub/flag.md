# Flag

used for reports

## sample payload

### mastodon

```http
POST /users/1/inbox HTTP/1.1
Host: friends.grishka.me
Content-Type: application/activity+json

{
	"@context": "https://www.w3.org/ns/activitystreams",
	"id": "https://mastodon.social/ccb4f39a-506a-490e-9a8c-71831c7713a4",
	"actor": "https://mastodon.social/actor",
	"content": "Please disregard this report. I'm just testing report federation.",
	"object": [
		"https://friends.grishka.me/users/1",
		"https://friends.grishka.me/posts/380590"
	]
}
```

notes:
- it is sent to the reported account's `inbox` -- [ReportService#forward_to_origin!](https://github.com/mastodon/mastodon/blob/c4a429ed47e85a6bbf0d470a41cc2f64cf120c19/app/services/report_service.rb#L51)
  - [arguably this should be sent to a `sharedInbox` if available? or an instance actor if detected somehow? idk]
  - [mastodon uses the wrong content-type currently -- [mastodon/mastodon#22720](https://github.com/mastodon/mastodon/issues/22720)]
- the first item in `object` is the user
- additional items in `object` are attached statuses
  - [shouldn't these be in `attachment` instead?]