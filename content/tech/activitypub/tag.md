+++
+++

# Tags

Tags are a property of Objects, stored in the `tag` array.

From <https://www.w3.org/TR/activitystreams-vocabulary/#dfn-tag>

**URI:** https://www.w3.org/ns/activitystreams#tag
**Notes:** One or more "tags" that have been associated with an objects. A tag can be any kind of Object. The key difference between attachment and tag is that the former implies association by inclusion, while the latter implies associated by reference.
**Domain:** Object
**Range:** Object | Link

{{<toc>}}

## Tagging Objects {#objects}

<p class="callout info">Not currently used in the fediverse.</p>

In theory, you could tag an Object to show that it is somehow referentially associated with the current Object.

An example application would be the Instagram or Facebook "tag a person in this image" feature, which might be implemented something like this:

```json
{
	"id": "https://social.example/objects/e9c427d8-cef1-48bd-ab89-59a6df29673b",
	"type": "Image",
	"url": "https://cdn.social.example/1578798509203652608.jpg",
	"tag": [
		"https://facebook.com/users/1"
	]
}
```

```json
{
	"id": "https://facebook.com/users/1",
	"type": "Person",
	"name": "Mark Zuckerberg"
}
```

However, it might be better to stick to using [Mentions](#mention) as defined below.

Another example would be tagging a Note or an Article, but the semantic meaning of this is unclear, at least for most current social use-cases.

## Tagging Links {#links}

In practice, The primary usage of `tag` in most current implementations is to establish a microsyntax. Microsyntaxes are substrings of `name`, `summary`, and `content` that should be linked to or replaced by some rich entity.

### Link {#link}

Tagging a Link is not currently widely used, as most use-cases are covered by a more specific sub-type.

#### Implementation details {#link-implementation}

In theory, the `name` of a Link could be used to represent the inner text of an anchor link, and the other properties of the Link (`href`, `rel`, etc) would likewise map onto the anchor link.

Consider the following Article:

```json
{
	"@context": [
		"https://www.w3.org/ns/activitystreams"
	],

	"id": "https://social.trwnh.com/about",
	"type": "Article",
	"content": "<marquee><p>My homepage is <a href=\"https://trwnh.com\" rel=\"me\">trwnh.com</a></p></marquee>",
	"tag": [
		{
			"type": "Link",
			"name": "trwnh.com",
			"href": "https://trwnh.com",
			"rel": ["me"]
		}
	]
}
```

For example, the Link in `tag` could be translated into `<a href="https://trwnh.com" rel="me">trwnh.com</a>"`. A plain-text implementation could strip all HTML tags, resulting in a plain-text `content` (`My homepage is trwnh.com`). It could then safely reconstruct only the marked-up links within the tag metadata. In doing so, the plain-text implementation has removed the use of any element that is not supported, such as `<marquee>`.

##### FEP-e232: Object Links {object-links}

FEP-e232 is an extension that proposes using `mediaType` to indicate when a Link refers specifically to an Object that can be fetched using ActivityPub. These Links to Objects are referred to as "object links". The `mediaType` is specified to be the Content-Type that MUST be used to fetch ActivityPub objects, per the ActivityPub spec.

In Misskey, a quote is a post that embeds a copy of another post below it. Using FEP-e232, a Misskey quote may be represented like so:

```json
{
	"@context": [
		"https://www.w3.org/ns/activitystreams"
	],

	"id": "https://example.com/@alice/statuses/1578798374936652608",
	"type": "Note",
	"content": "<p>This post is pretty cool <a href=\"https://trwnh.com/objects/e9c427d8-cef1-48bd-ab89-59a6df29673b\">RE: https://trwnh.com/objects/e9c427d8-cef1-48bd-ab89-59a6df29673b</a></p>",
	"tag": [
		{
			"type": "Link",
			"name": "RE: https://trwnh.com/objects/e9c427d8-cef1-48bd-ab89-59a6df29673b",
			"href": "https://trwnh.com/objects/e9c427d8-cef1-48bd-ab89-59a6df29673b",
			"mediaType": "application/ld+json; profile=\"https://www.w3.org/ns/activitystreams\"",
			"rel": ["https://misskey-hub.net/ns#_misskey_quote"]
		}
	]
}
```

(It has also been proposed that Misskey quotes be defined by a different extension that specifies a Quote type (as a sub-type of Link) and a `quotes` collection to keep track of related activities in the same vein as the `likes` and `shares` collections. See <https://socialhub.activitypub.rocks/t/fep-e232-object-links/2536/13> for more details.)

Other example microsyntaxes that could be represented by FEP-e232 include:

- Wiki (`link to [[article]]`, where `[[article]]` is an object link to another Article in the wiki)
- Imageboard (`>>6513 reply to post`, where `>>6513` is an object link to another Note in the thread)
- Issue tracker (`Merge request #123 fixes bug #13 `, where `#123` and `#13` are object links to other entities in the project)

General pseudocode may look something like this:

```py
# Extract custom emojis from tag array
tags = Object.tag
REQUEST_TYPE = 'application/ld+json; profile="https://www.w3.org/ns/activitystreams"'
RESPONSE_TYPE = 'application/activity+json'
ObjectLinks = [
	tag
	for tag in tags
	where tag.mediaType in set(REQUEST_TYPE, RESPONSE_TYPE)
]

for Link in ObjectLinks:

	# The purpose of object links is to know that you can fetch an object with ActivityPub:
	Object = http.GET(
		Link.href,
		headers={
			'Accept': REQUEST_TYPE
		}
	)

	# You can now process the object link in some way,
	# for example by creating deep links to be resolved within the local application
```

The text of FEP-e232 may be found at <https://codeberg.org/fediverse/fep/src/branch/main/feps/fep-e232.md>

### Mention {#mention}

A sub-type of Link that refers to an `@mention`, typically used for Actors. In practice, the Mention may begin with other characters depending on the sub-type of the Actor. For example, some implementations [TODO: which?] may use `@person` for Person actors and `!group` for Group actors.

#### Implementation details {#mention-implementation}

The `href` typically links to the `id` of the actor being mentioned.

Consider the following Note:

```json
{
	"@context": [
		"https://www.w3.org/ns/activitystreams"
	],

	"id": "https://example.com/@alice/hello-world",
	"type": "Note",
	"content": "<p>Hello @bob</p>",
	"tag": [
		{
			"type": "Mention",
			"name": "@bob",
			"href": "https://example.com/@bob"
		}
	]
}
```

<p class="callout danger">The following paragraph describes behavior necessary for Mastodon compatibility.</p>

In theory, this Mention tag is optional. In practice, Mastodon uses the presence of a Mention tag to determine when to send notifications, so it is not enough to include your primary recipients in the `to` field of your activity. You must also generate a Mention tag for them, *even if they are not actually mentioned anywhere in the text of `content`!* See for more information: <https://github.com/mastodon/mastodon/issues/11640> (and other related issues)

Mastodon also has other weird behavior regarding Mention tags. [TODO: figure out and compile a list of this implementation-specific behavior]

### Hashtag {#hashtag}

A sub-type of Link that refers to a `#topic`, typically used for associating the object with a collection of other objects sharing the same topic. 

#### Implementation details {#hashtag-implementation}

<p class="callout hint warning">Not officially part of the ActivityPub context definition, but still assumed to be included in the ActivityStreams `as:` namespace by most implementations (for historical reasons). Implementations should manually define `as:Hashtag` in their JSON-LD `@context`.</p>

The `href` typically links to a collection of all objects tagged with the same Hashtag.

Consider the following Note:

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    {
      "Hashtag": "as:Hashtag"
    }
  ],

  "id": "https://example.com/@alice/hello-world",
  "type": "Note",
  "content": "<p>I am talking about a #topic</p>",
  "tag": [
    {
      "type": "Hashtag",
      "name": "#topic",
      "href": "https://example.com/tagged/topic"
    }
  ]
}
```

### Emoji {#emoji}

<p class="callout warning">This is an extension type.</p>

See for more: <https://docs.joinmastodon.org/spec/activitypub/#emoji>

A sub-type of Link that refers to a `:custom_emoji:`, typically used for replacing a plain-text substring with an inline image (by implementations that do not otherwise support arbitrary inline images).

#### Implementation details {#emoji-implementation}

Handling an Emoji is typically done by matching for the Emoji's `name` and replacing with an `<img>` element where `src` is the `url` of the  Emoji's `icon`.

<p class="callout warning">Note that this extension uses `icon` instead of `href`, but the same "search-and-replace" semantics apply, as with any microsyntax.</p>

Consider the following Note:

```json
{
  "@context": [
    "https://www.w3.org/ns/activitystreams",
    {
      "toot": "http://joinmastodon.org/ns#",
      "Emoji": "toot:Emoji"
    }
  ],

  "id": "https://example.com/@alice/hello-world",
  "type": "Note",
  "content": "<p>Hello world :kappa:</p>",
  "tag": [
    {
      "id": "https://example.com/emoji/123",
      "type": "Emoji",
      "name": ":kappa:",
      "icon": {
        "type": "Image",
        "mediaType": "image/png",
        "url": "https://example.com/files/kappa.png"
      }
    }
  ]
}
```

- We extract the natural-language properties of `name`, `summary`, and `content`. In this case, we have only `content` of `Hello world :kappa:`.
- We extract any elements from `tag` with a type of `Emoji`. In this case, we have only one, for `:kappa:`.
- We search for `Emoji.name` (`:kappa:`) within the identified `content` (`Hello world :kappa:`) and replace it with an inline image sourced from `Emoji.icon.url` (`https://example.com/files/kappa.png`), resulting in the final value for content being `<p>Hello world <img src="https://example.com/files/kappa.png"/></p>`.

Pseudocode might look something like this:

```py
# Extract custom emojis from tag array
tags = Object.tag
Emojis = [tag for tag in tags where tag.type == "Emoji"]

for Emoji in Emojis:

  # replace :emoji: microsyntax with an inline image (within name)
  name = Object.name  # may need to extract `name` from `nameMap` instead
  name_matches = regex.match(substring = Emoji.name, text = name)
  for match in name_matches:
    search_and_replace(
      text = name,
      search = Emoji.name,
      replace = f'<img src={Emoji.icon.url}>'
      )

  # replace :emoji: microsyntax with an inline image (within summary)
  summary = Object.summary  # may need to extract `summary` from `summaryMap` instead
  summary_matches = regex.match(substring = Emoji.name, text = summary)
  for match in summary_matches:
    search_and_replace(
      text = summary,
      search = Emoji.name,
      replace = f'<img src={Emoji.icon.url}>'
      )

  # replace :emoji: microsyntax with an inline image (within content)
  content = Object.content  # may need to extract `content` from `contentMap` instead
  content_matches = regex.match(substring = Emoji.name, text = content)
  for match in content_matches:
    search_and_replace(
      text = content,
      search = Emoji.name,
      replace = f'<img src={Emoji.icon.url}>'
      )
```