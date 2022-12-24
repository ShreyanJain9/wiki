+++
+++

# Things you would need to do for compatibility

## Mastodon

### Unique username+domain pair

- webfinger domain.example for resource `acct:preferredUsername@domain` must link to your activitypub actor
  - mastodon will do this even when given a direct activitypub actor id
  - if webfinger is forwarded, mastodon will get the linked id and do the above

## Representing polls

- `Create Question` instead of just `Question` or `Create Note{attachment: Question}` (being treated like an object instead of an activity)
- Votes are `Create Note{name, inReplyTo: Note{attachment: Question}}` and the replied-to note must be local