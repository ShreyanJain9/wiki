+++
title = "Notifying remote servers that they should refetch an object"
date = "2019-11-01"
+++

{{toc}}

## Preserved text

<https://socialhub.activitypub.rocks/t/exposing-edit-history-via-activitystreams/2930>

@trwnh:

Per [expose edit history in ActivityPub representation · Issue #23292 · mastodon/mastodon · GitHub ](https://github.com/mastodon/mastodon/issues/23292)

@Johann150 raises a feature request for being able to view earlier versions of an activity or object before an Update was received. In the case where an object is Created and then Updated, someone receiving the Update who didn’t receive the Create first would have no way of knowing what was contained in the original version. Someone fetching the object without having received either of the activities would be able to know that it was edited based on the presence of the `updated` property, but likewise would not be able to retrieve any additional information about previous versions.

Looking at the existing Activity Vocabulary we see that the only existing way to do this would be in the case that `context` was published, although this requires that the context MUST be a Collection, and you would have to dig through all the items to filter for Update activities (plus the initial Create). Additional issues arise from the ordering of Collections not always being consistent, but in general you will still have to traverse the entire context collection.

Therefore it likely makes sense to propose a new property called something like `history` or `revisions`. If there is any prior art or some existing specifications that could apply here, it would be helpful to know about them.

## current thoughts

idk