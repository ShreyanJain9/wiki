+++
+++
# Actor

an actor is just something that has `inbox` and `outbox`

{{<toc>}}

## implementations

### mastodon

mastodon only understands Person / Group / Organization / Application / Service due to being overly strict -- [mastodon/mastodon#22322](https://github.com/mastodon/mastodon/issues/22322)

### pleroma

ostensibly pleroma is also limited in the same way? https://git.pleroma.social/pleroma/pleroma/-/blob/develop/lib/pleroma/constants.ex#L57

other references in code:

- admin api param `actor_types` only takes Person Service Application https://git.pleroma.social/pleroma/pleroma/-/blob/develop/docs/development/API/admin_api.md#L25

- Pleroma.Object.Containment#get_actor filters against Person Service Application https://git.pleroma.social/pleroma/pleroma/-/blob/develop/lib/pleroma/object/containment.ex#L22

- Pleroma.Web.ActivityPub.ActivityPub#maybe_update_follow_information checks that user type against Person Service https://git.pleroma.social/pleroma/pleroma/-/blob/develop/lib/pleroma/web/activity_pub/activity_pub.ex#L1608

- SimplePolicy checks for Application Group Organization Person Service https://git.pleroma.social/pleroma/pleroma/-/blob/develop/lib/pleroma/web/activity_pub/mrf/simple_policy.ex#L230

- openapi spec schema defines enum Application Group Organization Person Service https://git.pleroma.social/pleroma/pleroma/-/blob/develop/lib/pleroma/web/api_spec/schemas/actor_type.ex#L11

### misskey

the isActor check only validates if one of the five types

the interface requires `inbox`, `outbox` so that's fine, it should just drop the `type` hardcoded check

https://github.com/misskey-dev/misskey/blob/22ccb0fa716a84560c8599781647baaaeb8e80bd/packages/backend/src/core/activitypub/type.ts#L150-L176

```js
export const validActor = ['Person', 'Service', 'Group', 'Organization', 'Application'];


export const isActor = (object: IObject): object is IActor =>
	validActor.includes(getApType(object));


export interface IActor extends IObject {
	type: 'Person' | 'Service' | 'Organization' | 'Group' | 'Application';
	name?: string;
	preferredUsername?: string;
	manuallyApprovesFollowers?: boolean;
	discoverable?: boolean;
	inbox: string;
	sharedInbox?: string;	// 後方互換性のため
	publicKey?: {
		id: string;
		publicKeyPem: string;
	};
	followers?: string | ICollection | IOrderedCollection;
	following?: string | ICollection | IOrderedCollection;
	featured?: string | IOrderedCollection;
	outbox: string | IOrderedCollection;
	endpoints?: {
		sharedInbox?: string;
	};
	'vcard:bday'?: string;
	'vcard:Address'?: string;
}
```

### gotosocial

https://github.com/superseriousbusiness/gotosocial/blob/d445c60a26e7f51f6d742e992d15cb5fabe2100c/internal/federation/dereferencing/account.go#L412-L478

seems to have a switch-case for Application Group Organization Person Service, but the comment above dereferenceAccountable() says this only (currently?) works for Person Application Service

### peertube

https://github.com/Chocobozzz/PeerTube/blob/develop/shared/models/activitypub/activitypub-actor.ts

seems to have required fields `type` `id` `following` `followers` `inbox` `outbox` `preferredUsername` `url` `name` `endpoints.sharedInbox` `summary` `attributedTo` `publicKey` `publicKey.id` `publicKey.owner` `publicKey.publicKeyPem` [whew that's a lot!]

other code spots:

https://github.com/Chocobozzz/PeerTube/blob/5070a9956052ed494077bb5e308eedd13e964799/server/helpers/custom-validators/activitypub/actor.ts

sanitizeAndCheckActorObject() does the following:

- actor exists
- id is valid AP url
- inbox is valid AP url
- preferredUsername is valid
- url is valid AP url
- publicKey is valid public key object
- endpoints is valid endpoints object
- either no outbox, or outbox is valid AP url
- either no following, or following is valid AP url
- either no followers, or followers is valid AP url
- set valid attributedTo
- set valid description [summary???]
- either type is not Group, or [if type is Group] then attributedTo has at least 1 item