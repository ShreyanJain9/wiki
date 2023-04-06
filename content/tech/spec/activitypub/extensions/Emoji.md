### Emoji {#emoji}

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