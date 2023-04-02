+++
title = "understanding activitypub as a set of normative requirements and recommendations"
toc = true
autonumbering = true
+++

## what is activitypub

in the simplest terms? "email for websites". you POST to inbox or outbox. the serialization of messages is activitystreams 2.0 (as2), and certain activities have defined side-effects when received in the inbox or outbox. client-to-server (c2s) uses the outbox, and server-to-server (s2s) uses the inbox.

### theoretical foundation for activitypub

- actor model. an actor is something that is programmed to behave in a certain way.
- message passing. the way actors communicate is to pass messages around, which are handled according to local procedures programmed into the actor.
- ontology. the nature of being. this is done with a resource description framework (RDF) where you have a set of statements (facts) about each resource.
- open world assumption. in logic systems, facts are not necessarily known by everyone.

## activitystreams 2.0 as a foundation for activitypub

AS2 documents represent resources and are described with properties. JSON-LD is used as a serialization of RDF.

### requirements

technical:

- 2: empty arrays are explicitly set to `null` or excluded entirely
- 2: use UTF-8
- 2.1: equivalent to JSON-LD compaction against `{"@context": "https://www.w3.org/ns/activitystreams"}`
- 2.1: do not override definitions from `https://www.w3.org/ns/activitystreams`
- 2.1: if `@context` is missing, assume or inject `https://www.w3.org/ns/activitystreams`
- 2.2: any international links (IRIs) should be converted to ascii (URIs) unless used as an `id`
- 2.3: datetimes use RFC3339, and use a "Z" if there is no timezone offset
- 4.1, 4.3, 4.4: if you use an extension type that overlaps with a core vocab type, do not exclude the core type from the `type` array [e.g. a vcard `Individual` should also explicitly be an as2 `Person`]
- 4.1: prefer as2 vocab when properties overlap [e.g. use as2 `name` instead of schema.org `name`]
- 4.2: Link `rel` must be valid in both RFC5988 and in HTML5 (registered or unregistered)
- 4.7: for language maps (`nameMap` /`summaryMap` / `contentMap`), every key is a well-formed BCP47 language tag, and every value is a string
- 4.7.2: properly handle bidirectional text
- 5: if you encounter something you don't understand, ignore it and continue processing as normal. do not stop or error.
- 5.1: if you want to support extensions, support compact iri expansion [i.e. `prefix:term` should be expanded according to the definition for `prefix`] for properties and `@id` values.
- 8.1: if using `application/activity+json; profile=""` then the list of profiles must be quoted [inside the double quotes]. `application/json` also still applies.
- 9.1: do not use deprecated as1 vocab. use as2 vocab. if you use other vocabs then also use as2 still. do not use json-ld algorithms other than compaction. use json-ld for extensions.

non-technical / restatements:

- 6, 7: tell your users when and why their personal info is needed/published. do not re-emit malicious input you consume [xss, etc]
- 9.2.1: conforming publishers make as2 documents following as2 serialization. consider privacy. consider security.
- 9.2.2: conforming consumers tolerate deprecated or obsolete as1 props. ignore props or types that aren't applicable to you. faithfully translate info when presenting on screen, in print, in audio, etc. consider privacy and security.
- B.2: `application/stream+json` or generic `application/json` is processed with old as1 rules. as2 only applies when using `application/activity+json`


### recommendations

technical:

- 1.2: do not use `displayName`, `verb`, `title`, or `objectType`. if encountered, process them using the deprecated AS1 syntax
- 2.1: include a `@context` property even if you don't process JSON-LD
- 2.2: do not use relative IRIs; plain json parsers have trouble with this
- 4.1, 4.3, 4.4: take care not to overlap or duplicate existing types
- 4.1.1: `name` is derived from user input. if not present, then `summary` should be plaintext and derived from user input, as well as being short enough to use as a reasonable text representation of the object
- 4.1.1: have a fallback strategy in case `name` and `summary` are missing or not available in your user's language or are too long
- 4.3: use vcard for describing Person, Group, Organization
- 4.6: if you wanna reconstruct a paged OrderedCollection, go to the first/last page and follow the next/prev links until all pages have been processed
- 4.6: OrderedCollection should use OrderedCollectionPage to maintain relative ordering
- 4.7.2: publishing bidirectional text should use explicit unicode control character or HTML
- 4.7.2: consumers should identify base direction of text
- 4.8: explicitly mark the language for natural language properties if the language is known (using either maps or default `@language` tag)
- 5: if you want to support extensions, use JSON-LD. define all extension term in `@context`
- 5.1: avoid compact IRIs except for property names and for types
- 5.2: if you use LD and reserialize an as2 document, preserve any properties/extensions you don't understand (by compacting against the original `@context` instead of your own)
- 8: consider `application/ld+json; profile="https://www.w3.org/ns/activitystreams"` as being equivalent to `application/activity+json`

non-technical:

- 6: limit sensitive personal info unless users "opt in"
- 6: do not store or share personal info unless users "opt in"
- 7: prevent spam and malicious content
- 7: do not publish malicious input from users
- 7: use rel=nofollow or convert links to plaintext to avoid SEO loopholes
- 7: be aware of spoofing attacks
- B: don't output AS1 unless you want to remain back-compatible
- B.9: avoid using `upstreamDuplicates` and `downstreamDuplicates` from AS1
- B.10: if converting as1 to as2, treat `post` as `Create`, or if `target` is used, then treat it as `Add`


### optional

- 2.1: @context extensions are allowed before compacting
- 2.1: using properties and values not defined in @context is fine but will likely be ignored by LD consumers
- 2.1: using http instead of https for the activitystreams uri
- 2.2: using uris instead of iris (since uris are valid iris)
- 2.3: omitting seconds from datetimes
- 4.1.1: not including `name` and `summary`, or having no explicit value in your current language, or being longer than appropriate for text representations
- 4.2: using Link `rel` that is not registered
- 4.6: using `OrderedCollection` to identify Collections whose items are ordered (since Collections may or may not be ordered)
- 4.6.1: using `OrderedCollectionPage` for collection pages whose items are ordered. using `startIndex` on these.
- 4.7.1: using `@language` inside `@context` to define default language. (this might not be understood by non-LD-aware consumers)
- 4.7.2: using bidirectional text. having the base direction change. wrapping additional control characters before display.
- 7: consider spam and malicious content
- 7: convert untrusted links to plaintext or add `rel=nofollow`
- 9.2.2: republish or present any consumed document in another format or presentation mechanism
- B.9: using `upstreamDuplicates` and `downstreamDuplicates` from AS1

## as2 vocab

### requirements

- 1: be able to serialize and deserialize all extended properties even if you don't understand them
- 1.1: all `xsd:datetime` MUST conform to rules in AS2 (RFC3339)
- 3: avoid using extensions that heavily overlap with or duplicate the as2 vocab
- Question: MUST NOT have both `anyOf` and `oneOf`
- name: MUST NOT include HTML markup
- duration: MUST be xsd:duration as defined by xmlschema (e.g. `P15D`, `PT2H`, etc)
- hreflang: MUST be a BCP47 language tag
- rel: MUST conform to both RFC5988 and HTML5 (cannot contain space, tab, LF, FF, CR, or comma)
- 5.1: remove `bto` and `bcc` before redistributing. they are part of the primary/secondary audience, but not disclosed to anyone else.
- Place: (non-normative) MUST support `name` `longitude` `latitude` `radius` `altitude` `accuracy` even if other mechanisms are used. [you don't have to use all of them but you have to support all of them]

### recommendations

- icon: has a square (1:1) aspect ratio and is suitable for presentation at small sizes
- 5.1: leave `to` and `cc` intact if redistributing an object
- 5.6: (non-normative) don't require parsing `name`/`summary`/`content` to determine notification, categorization, or linking. use vocab explicitly for this purpose [e.g. use `to` or `tag` instead of depending on text containing `@sally` or `#givingthanks`]
- 5.6: the primary audience in `to` receives notifications

### optional

- Question: use `anyOf` or `oneOf` to express possible answers
- type: multiple values can be specified
- actor: could be an indirect Link, could have multiple values
- content, name, summary: can use multiple language-tagged values in a map (`contentMap`, etc)
- 5.1: use `to`, `cc`, `bto`, `bcc` as explicit primary and secondary audiences. use `bto` and `bcc` for privately targeting.
- 5.2: reuse existing vocabularies for describing relationships, such as FOAF or Relationship vocabs, or create your own
- 5.3: (non-normative) using other mechanisms for describing locations other than `Place`
- 5.6: (non-normative) using microsyntaxes within `content`, `name`, `summary`
- 5.2.1: (non-normative) using `result` to include side effects of `Accept`
- 5.2.1: (non-normative) using `context` to relate activities back to a common reference point and efficiently group related activities together for display or analysis


## activitypub

2.1 describes profiles for c2s "social API" and s2s "federation protocol". a conformant Client does all c2s, a conformant Server does all c2s, a conformant Federated Server does s2s

activitypub follows as2-core and as2-vocab "core classes" of Object/Link/Activity/Collection/etc

### requirements

- 3.1: persistent distributed objects have unique global `id`
- 3.1: use `id` in s2s for persistent distributed objects
- 3.2: c2s Servers present AS2 in response to `application/ld+json; profile="https://www.w3.org/ns/activitystreams"`
- 3.3: Clients specify `Accept: application/ld+json; profile="https://www.w3.org/ns/activitystreams"`
- 4.1: actors have `inbox` and `outbox`
- 4.1: dereferencing `sharedInbox` only includes Public activities
- 5: OrderedCollection is reverse chronological (newer items first)
- 5.1: `outbox` is an `OrderedCollection`
- 5.2: `inbox` is an `OrderedCollection`
- 5.2: Server deduplicates activities in inbox. (duplication might happen when an activity is addressed to a follower both implicitly via collection and explicitly via direct id.) dedupe by `id` and drop any existing activities
- 5.3: `followers` is `OrderedCollection` or `Collection` (if present)
- 5.4: `following` is `OrderedCollection` or `Collection` (if present)
- 5.5: `liked` is `OrderedCollection` or `Collection` (if present)
- 5.6: do not attempt to POST to `Public`
- 5.7: `likes` is `OrderedCollection` or `Collection` (if present)
- 5.8: `shares` is `OrderedCollection` or `Collection` (if present)
- 6: Clients discover your `outbox` and HTTP POST with `Content-Type: application/ld+json; profile="https://www.w3.org/ns/activitystreams"` using authentication with your credentials. the body is a single Activity or a single non-Activity object that will be wrapped in a Create
- 6: c2s Servers ignore any Client-provided `id` and generate their own
- 6: c2s Servers return 201 Created with `Location: <id>` header
- 6: c2s Servers remove `bto`/`bcc` before delivery but after calculating recipients for delivery
- 6: c2s Servers must add this Activity to the `outbox` collection [at least until side effects are processed]
- 6.1, 7.1.1: Clients be aware that Servers will only deliver to `to`, `cc`, `bto`, `bcc`, `audience`
- 6.1: Clients provide `object` for Create, Update, Delete, Follow, Add, Remove, Like, Block, Undo. provide `target` for Add, Remove.
- 6.2.1: wrap valid objects that aren't Activities in a Create. assign `id` to both the Object and the Create (unless transient). copy over `to`, `cc`, `bto`, `bcc`, `audience` from the wrapped object into the wrapping Create. return the id of the Create, not the object. [the object id is available via `object.id`]
- 6.3: c2s Update modifies the `object` if the actor has permission
- 6.10: c2s Undo has `Undo.actor` == `Undo.object.actor`
- 6.11: c2s Server that is also s2s Federated Server follows 7.1.1 outbox delivery
- 7: POST inbox has `Content-Type: application/ld+json; profile="https://www.w3.org/ns/activitystreams"`
- 7: GET has `Accept: application/ld+json; profile="https://www.w3.org/ns/activitystreams"`
- 7: s2s Servers provide `object` for Create, Update, Delete, Follow, Add, Remove, Like, Block, Undo. provide `target` for Add, Remove.
- 7.1: s2s Servers that deliver to Collection MUST dereference the collection with the user's credentials [[how do they have this???]] and discover inboxes for each item in the collection. limit layers of indirection through additional Collections inside Collections
- 7.1: dedupe the final recipient list
- 7.1: exclude `actor` from the final list (i.e. don't deliver to yourself)
- 7.1.2: inbox forward to `to`, `cc`, `audience` iff first time seeing + value of `to`, `cc`, `audience` is a Collection owned by the server + `inReplyTo`, `object`, `target`, `tag` are owned by the server. don't pick up any new addressees outside of `to`, `cc`, `audience`
- 7.1.3: delivery to `sharedInbox` still delivers to `inbox` for actors that don't have a `sharedInbox`
- 7.3: ensure s2s Update is authorized to modify `object` (at minimum by applying same-origin check)
- 7.5, 7.7: Reject Follow MUST NOT add actor to followers collection

### recommendations

- 3: include activitystreams @context
- 3: validate anything you receive to avoid spoofing (exact mechanism is out-of-scope, but if it is resolvable then you can fetch the `id`)
- 3.1: use HTTPS `id` for public content
- 3.1: allocate `id` in actor's namespace
- 3.2: present AS2 in response to `application/activity+json`
- 3.2: authorization checks fail with an appropriate HTTP error code (at least 403 if private)
- 3.3: Clients warn users if editing will overwrite `source`
- 4: normalize `id` when entered in ui or login form. URIs are used directly, and if normalization fails, consider it invalid. dereference the actor URI once identified
- 4.1, 5.3, 5.4: actors have `following`, `followers`
- 4.1: `sharedInbox` is a publicly readable `OrderedCollection` containing Public addressed activities
- 5: don't use "last updated" timestamp to order items in an OrderedCollection (bc it changes too much)
- 5.1: unauthenticated requests to `outbox` return all Public posts
- 5.2: filter `inbox` according to requester permissions
- 5.2: if you don't support s2s Federated Server profile, then respond to POST inbox with 405 Not Allowed
- 5.6: plain JSON consumers, be aware of equivalence of Public, as:Public, etc
- 6: POST to outbox which doesn't support c2s Server should respond with 405 Not Allowed
- 6: respect HTTP caching for Clients and Servers
- 6.1: consider `actor`/`attributedTo` of `object`/`target`/`inReplyTo`/`tag[*]` for additional recipients. if you recurse through these, set a limit for recursion (without necessarily unpacking collections)
- 6.2: Create should copy `actor` to `object.attributedTo`
- 6.2: reconcile audiences on object and Create for initial distribution [although the object can be updated to have a different audience later]
- 6.2.1: return `Location: <id of Create>` instead of id of object
- 6.3.1: c2s partial Update removes any property set to `null`. s2s Update is complete.
- 6.4: respond with 410 Gone if a Tombstone is presented, otherwise 404
- 6.5: c2s Follow adds `Follow.object` to `Follow.actor.following` iff/when an Accept Follow is received
- 6.6, 6.7: c2s Add/Remove inserts/removes `object` to/from `target` unless `target` is not owned/authorized, or unless `object` is not allowed to be added/removed to `target` (at discretion of the c2s Server)
- 6.8: c2s Like adds `object` to `actor.liked` if present
- 6.9: c2s Block prevents `object` from interacting with any `object.attributedTo == Block.actor` or `actor == Block.actor`
- 6.9: c2s Block is not delivered to `object`
- 6.10: c2s Undo will undo any side effects (e.g. Like, Follow, Block) to the extent possible [e.g. Undo Like will remove the Like from the `likes` collection and decrement the `totalItems` counter]
- 6.10: don't use c2s Undo where an "inverse activity" exists, e.g. Create->Delete or Add->Remove
- 7: any persistent distributed activity SHOULD have an `id`
- 7: s2s Federated Servers interpret a `Content-Type` or `Accept` header of `application/activity+json` as equivalent to `application/ld+json; profile="https://www.w3.org/ns/activitystreams"`
- 7: respect HTTP caching for Federated Servers
- 7.1: s2s POST to inbox returns 405 Not Allowed if not an s2s Federated Server
- 7.1: use async for delivery
- 7.1: retry delivery after network failure
- 7.1: in inbox forwarding, recurse through `inReplyTo`, `object`, `target`, `tag` (with a max recursion limit) to look for "linked objects owned by the server" [this is presented in context of "getting updates that ... involve the recipient", but i can't see this being useful when we have explicit audience/addressing, except maybe for notification policies?]
- 7.2: s2s Create appears in `inbox`
- 7.3: s2s Update replaces copy of `id == object.id` with new repr
- 7.4: s2s Delete removes copy of `id == object.id`
- 7.5: s2s Follow generates either Accept/Reject Follow delivered to `actor`. upon [sending] Accept, add the `Follow.actor` to `actor.followers` collection
- 7.6: s2s Accept Follow (where the `Follow` is one you previously sent) adds `Accept.actor` to `Follow.actor.following`
- 7.8, 7.9: s2s Add/Remove adds/removes the `object` to/from `target` unless `target` isn't owned by you(r server) or unless `object` cannot be added/removed to `target` for some other reason (at receiver's discretion)
- 7.10: s2s Like gets added to `object.likes` if present
- 7.11: s2s Announce gets added to `object.shares` if present
- A: use internationalization tooling whenever possible [i.e. setting `@language` or using map properties if a language is defined or detected]
- B.2: (non-normative) Servers do not trust Client content, and Federated Servers do not trust other Federated Servers without verification or same-origin policy. be careful to verify attribution and write permissions.
- B.4: (non-normative) check how your URI handling library handles things like `file://` and consider whitelisting only `http`/`https`/etc as needed
- B.5: (non-normative) set a limit on recursion to avoid DoS
- B.7: (non-normative) implement protections against DoS from other Federated Servers, such as rate limiting. be especially careful whenever there are side effects to activities. do not overload other Federates Servers with activities (e.g. use exponential backoff strategy)
- B.8: (non-normative) rate limit Client submissions in order to prevent DoS of Server and also to ensure it does not propagate a DoS to Federated Servers
- B.9: (non-normative) limit the size of Collection pages when requested by Clients. Clients should also prepare to self-limit by e.g. timing out or erroring if they connect to a malicious Server that serves oversized collections
- B.11: (non-normative) omit `bto` and `bcc` when displaying objects even if not delivered

### optional

- 2.1: servers may implement either c2s or s2s individually
- 3: include additional `@context` other than activitystreams
- 3.1: omit `id` from transient objects
- 3.2: dereference `id` with HTTP GET
- 3.2: servers MAY use content negotiation
- 3.2: support other behaviors such as additional protocols or HTML response
- 3.2: require authorization, and implement your own rules on top of B.1
- 3.2: use 404 Not Found to not leak existence of private objects without authorization
- 4: clients normalize provided `id` by assuming a default scheme, preferably `https`
- 4.1, 5.5: actors have `liked`
- 4.1: actors have `streams`, `preferredUsername`, `endpoints`, `endpoints.proxyUrl`, `endpoints.oauthAuthorizationEndpoint`, `endpoints.oauthTokenEndpoint`, `endpoints.provideClientKey`, `endpoints.signClientKey`, `endpoints.sharedInbox`
- 5.3, 5.4, 5.5, 5.7: filter `followers`, `following`, `liked`, `likes`, `shares` based on authenticated user or unauthenticated request
- 5.6: `sharedInbox` is for public and followers-only posts
- 5.7: objects have `likes`
- 5.8: objects have `shares`
- 6: interpret C2S `application/activity+json` as equivalent to C2S `application/ld+json; profile="https://www.w3.org/ns/activitystreams"` when used as `Accept` or `Content-Type`
- 6: activities POSTed to c2s outbox can contain embedded objects
- 6: c2s Servers carry out side effects, and the activity might appear later or disappear at any time from outbox
- 6.1: Clients MAY get inbox from additional recipients, MAY recurse through attached objects, MAY allow users to amend their addressing to include these recipients
- 6.4: c2s Server MAY take a `Delete` and replace the `object` with a `Tombstone`
- 6.12: (non-normative) support uploading media using some out-of-scope mechanism
- 7: s2s transient activities can omit `id`
- 7.1: limit indirection of Collection -> Actor to one layer
- 7.1.2: filter inbox forwarding delivery targets according to impl-specific rules like spam filtering
- 7.1.3: use `sharedInbox` to dedupe deliveries of Public activities or activities whose delivery will be determined by the receiving server
- 7.1.3: deliver Public activities to all known `sharedInbox` endpoints
- 7.3: apply same-origin check to `Update.id` and `Update.object.id`
- 7.4: replace `Delete.object` repr with a `Tombstone`
- 7.5: auto-generate Accept/Reject Follow, or require user input
- 7.5: do not send Reject Follow (though this might leave the server in an intermediate state)