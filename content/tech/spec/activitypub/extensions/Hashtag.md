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