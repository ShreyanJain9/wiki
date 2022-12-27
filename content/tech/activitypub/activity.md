+++
+++

# Activity

an Activity is just an object that has an `actor`

{{<toc>}}

## what does it mean to be an Activity

for C2S: do not wrap it in a Create when it gets POSTed to outbox

## implementations

### mastodon

supported activity types <https://github.com/mastodon/mastodon/tree/main/app/lib/activitypub/activity>

- Accept (accept a follow request)
- Add (adds hashtag or status to featured collection)
- Announce (reblog a status)
- Block (block an account)
- Create (create a message or status)
- Delete (delete account or status)
- Flag (report accounts and/or statuses)
- Follow (request to follow a local account)
- Like (favourite a status)
- Move (migrate an account's followers)
- Reject (reject follow request or remove follower)
- Remove (removes hashtag or status from featured collection)
- Undo (undoes some activities if possible)
- Update (update account info or edit a status)

#### extended logic

- **Accept**
	- **Accept Follow** -- authorize a follow request
		- (when relay is findable by `object` as follow_activity_id)
		- (when `object` is uri of existing follow request)
		- (when `object` is embedded Follow)
			- **Accept Follow `<Account>`**
- **Add**
	- (must have `target`)
	- (`target` must be a `toot:featured` collection)
	- (`object` must be inlined?)
	- **Add Hashtag to `target<featured>`** -- feature a hashtag
		- (takes `object.name` and removes prefix `#`)
		- (creates FeaturedTag entity for the `actor`'s account)
	- **Add `object<Status>` to `target<featured>`** -- pin a status
		- (`object` must be transformable to status)
		- (status must be authored by `actor`)
		- (status must not already be pinned)
- **Announce**
	- (must be related to local activity)
		- (i.e. `actor<Account>` followed by local accounts)
		- (or requested through relay)
		- (or `object` refers to a status whose account is local)
	- (`object` resolves to a status)
	- **Announce `object<Status>`** -- creates a reblog of a status
		- (stop if:)
			- (no status)
			- (status not boostable)
			- (requested through relay)
		- (find existing reblog or create)
- **Block**
	- (`object` resolves to an account)
		- (the resolved account exists and is local)
	- **Block `object<Account>`** -- block an account
		- (unfollow the `object<Account>`)
		- (force `object<Account>` to unfollow you)
		- (reject any follow requests from `object<Account>`)
		- (create a block asynchronously)
- **Create**
	- (dereference the `object`)
	- **Create EncryptedMessage** -- creates an encrypted message
		- (tries to find a device id)
		- (extracts `messageType`, `cipherText`, `digest`, `messageFranking`)
	- **Create `object<Status>`** -- creates a status
		- (rejected if)
			- (unsupported object type)
				- (`object` is a string)
				- (not in supported types)
					- Note
					- Question
				- (not in converted types)
					- Image
					- Audio
					- Video
					- Article
					- Page
					- Event
			- (or invalid origin)
			- (or tombstone exists for `object` uri)
			- (or not related to local activity)
- **Delete**
	- (if `actor` matches `object`)
		- **Delete `object<Account>`** -- delete your own account
	- **Delete `object<Status>`** -- delete a status
		- (create tombstone unless origin is invalid)
		- (forward deletion if forwardable)
		- (remove status from database and timelines)
- **Flag**
	- (`object` array is resolved as accounts and statuses)
		- (then filtered for local)
	- **Flag `object<Account,Status>`** -- report an account and/or statuses
		- (foreach account get reported statuses)
		- (create a report against the account w/ optional status ids and content)
- **Follow**
	- (`object` must be a local account that exists)
	- **Follow `object<Account>`** -- request to follow a local account
		- (create or update follow request)
		- (auto-reject if:)
			- (`object<Account>` is blocking `actor<Account>`)
			- (`object<Account>` is domain-blocking `actor`'s domain)
			- (`object<Account>` has moved/redirected)
			- (`object<Account>` is the instance actor)
		- (send an Accept Follow if a follow already exists)
		- (otherwise create a follow request)
			- (if you are silenced or they are locked, send frq notification)
			- (otherwise send Accept Follow and follow notification)
- **Like**
	- (resolve status from `object`)
	- **Like `object<Status>`** -- favourite a status
		- (stop if:)
			- (no status resolved)
			- (status is not from a local account)
			- (delete arrived first)
			- (you already favourited this status)
		- (create a favourite by `actor<Account>` for `object<Status>`)
- **Move**
	- (`actor` must match `object` and resolve to account)
	- (resolve `target` as account)
	- **Move `object<Account>` to `target<Account>`** -- migrate your followers
		- (stop if:)
			- (no `target<Account>`)
			- (`target<Account>` is suspended)
			- (`target.alsoKnownAs` doesn't include `object`)
		- (set a redirect)
		- (async move followers from `object<Account>` to `target<Account>`)
- **Reject**
	- **Reject Follow** -- reject a follow request or remove follower
		- (when relay is findable by `object` as follow_activity_id)
		- (when `object` is uri of existing follow request)
		- (when `object` is uri of existing follow)
			- (call UnfollowService for `actor<Account>`)
		- (when `object` is embedded Follow)
			- **Reject Follow `Follow.object<Account>`**
- **Remove**
	- (must have `target`)
	- (`target` must be a `toot:featured` collection)
	- (`object` must be inlined?)
	- **Remove Hashtag from `target<featured>`** -- unfeature a hashtag
		- (takes `object.name` and removes prefix `#`)
		- (deletes FeaturedTag entity for the `actor`'s account)
	- **Remove `object<Status>` from `target<featured>`** -- unpin a status
		- (`object` must be transformable to status)
		- (status must be authored by `actor`)
		- (destroy pin if existing pin found)
- **Undo**
	- (`object` should be inlined or else it may not be handled)
	- **Undo Announce** -- unreblog a status
		- (stop if no `Announce.id`)
		- (resolve `Announce.id` as status reblog)
		- (call RemoveStatusService on status)
	- **Undo Accept** -- unfollow an account [yes this is the assumption]
		- (find follow by `object.object.id` and revoke it)
	- **Undo Follow**
		- (resolve `object.object` as account)
			- (stop if not exists or not local)
		- **Undo Follow `<Account>`** -- unfollow an account
			- (`Undo.actor` unfollows `Follow.object<Account>` if following)
	- **Undo Like**
		- (resolve `object.object` as status)
		- (stop if no status or if status is not local)
		- **Undo Like `<Status>`** -- unfavourite a status
			- (if `actor` has favourited the status:)
				- (find a favourite on that status by that account)
				- (destroy it)
	- **Undo Block**
		- (resolve `object.object` as account)
		- (stop if not exists or not local)
		- **Undo Block `<Account>`** --unblock an account
			- (call UnblockService if `actor` is blocking `Block.object<Account>`)
	- **Undo `object<IRI>`**
		- (try_undo_announce)
			- (find reblogged status by `object` uri and `actor`)
			- (if found, remove reblog and stop, else continue)
		- (try_undo_accept)
			- (do nothing, continue) [because Accept uri is not stored]
		- (try_undo_follow)
			- (find `actor<Account>` follow request/relation by `object` uri)
			- (if found, destroy it and stop, else continue)
		- (try_undo_like)
			- (do nothing, continue) ["too costly", no index by Like uri]
		- (try_undo_block)
			- (find `actor<Account>` block by `object` uri)
			- (if found, call UnblockService and stop, else continue)
- **Update**
	- (dereference `object`)
	- (if `object.type` in `Application Group Organization Person Service`)
		- **Update `object<Account>`** -- update an account's info
			- (stop if `actor.id` does not match `object.id`)
			- (call ProcessAccountService)
	- (else if `object.type` in `Note Question`)
		- **Update `object<Status>`** -- edit a status
			- (stop if invalid origin)
			- (resolve `object` as status)
			- (stop if no status)
			- (call ProcessStatusUpdateService)