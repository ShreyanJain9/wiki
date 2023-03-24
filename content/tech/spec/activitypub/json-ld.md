+++
autonumbering = true
+++

# everything you need to know about JSON-LD as far as activitypub is concerned

{{<toc>}}

## IRIs

basically just URI+. the i stands for "internationalized"

## the playground

<https://json-ld.org/playground/> is a good way to get a better understanding of stuff

## json-ld keywords

there are [a lot](https://www.w3.org/TR/json-ld11/#syntax-tokens-and-keywords "no, seriously") but we only really care about @id and @context really (and sometimes @type)

### @id

the identifier for a json-ld node. by "node" we mean like a node on a graph. typically this is an iri, and dereferencing the iri should give us that node.

### @context

basically a way to map shorthand names ("terms") to a full iri, or sometimes to a keyword. you can also define iris or compact iris using a json object that represents the expanded definition.

in short:
- terms (`foo`) map to iris (`https://example.com/foo`), compact iris (`ex:foo`), or keywords (`@foo`)
- iris (whether full like `https://example.com/foo` or compact like `ex:foo`) map to expanded definitions (a json object with only keywords inside)

#### namespacing

the benefit of the context definition is that we can namespace different json-ld terms based on which vocabulary they came from

the activitystreams namespace (or base iri, if you will) is `https://www.w3.org/ns/activitystreams#` because this is what all activitystreams vocab terms begin with

- consider the `actor` property, whose full iri is actually `https://www.w3.org/ns/activitystreams#actor`
- similarly, consider the `Public` collection, whose full iri is `https://www.w3.org/ns/activitystreams#Public`
- ...and so on

we can also map some arbitrary string ("term") to the base iri. the activitystreams context by default uses `as` => `https://www.w3.org/ns/activitystreams#`. so instead of typing out the base iri every single time, we can instead type the *shorthand* prefix `as:`, or in other words, `as:actor` or `as:Public`, to get what is known as a "compact IRI"

note once again the equivalencies:

- `actor` == `as:actor` == `https://www.w3.org/ns/activitystreams#actor`
- `Public` == `as:Public` == `https://www.w3.org/ns/activitystreams#Public`
- ...and so on

## the three ways to normalize you should be familiar with

let's do expansion, compaction, flattening of the following example document:

```json
{
	"@context": [
		"https://www.w3.org/ns/activitystreams",
		{
			"Hashtag": "as:Hashtag",
			"toot": "https://joinmastodon.org/ns#",
			"Emoji": "toot:Emoji"
		}
	],
	"id": "https://social.example/~alice/notes/1",
	"type": "Note",
	"content": "#HelloWorld :blobcatHug:",
	"tag": [
		{
			"type": "Hashtag",
			"name": "#HelloWorld",
			"href": "https://social.example/tags/HelloWorld"
		},
		{
			"type": "Emoji",
			"name": "blobcatHug",
			"icon": {
				"type": "Image",
				"mediaType": "image/png",
				"url": "https://social.example/emojos/blobcatHug.png"
			}
		}
	]
}
```

and let's assume we only understand the following context:

```json
{
	"@context": "https://www.w3.org/ns/activitystreams"
}
```

### expansion

expansion works by taking the context and expanding every single term to its full iri form, and every single value into an array of objects with either a single @value (if the value before was a literal) or a single @id (if the value before was an iri)

so if we expand the example document, we get this:

```json
[
  {
    "https://www.w3.org/ns/activitystreams#content": [
      {
        "@value": "#HelloWorld :blobcatHug:"
      }
    ],
    "@id": "https://social.example/~alice/notes/1",
    "https://www.w3.org/ns/activitystreams#tag": [
      {
        "https://www.w3.org/ns/activitystreams#href": [
          {
            "@id": "https://social.example/tags/HelloWorld"
          }
        ],
        "https://www.w3.org/ns/activitystreams#name": [
          {
            "@value": "#HelloWorld"
          }
        ],
        "@type": [
          "https://www.w3.org/ns/activitystreams#Hashtag"
        ]
      },
      {
        "https://www.w3.org/ns/activitystreams#icon": [
          {
            "https://www.w3.org/ns/activitystreams#mediaType": [
              {
                "@value": "image/png"
              }
            ],
            "@type": [
              "https://www.w3.org/ns/activitystreams#Image"
            ],
            "https://www.w3.org/ns/activitystreams#url": [
              {
                "@id": "https://social.example/emojos/blobcatHug.png"
              }
            ]
          }
        ],
        "https://www.w3.org/ns/activitystreams#name": [
          {
            "@value": "blobcatHug"
          }
        ],
        "@type": [
          "https://joinmastodon.org/ns#Emoji"
        ]
      }
    ],
    "@type": [
      "https://www.w3.org/ns/activitystreams#Note"
    ]
  }
]
```

the point of expansion is to end up with a document that is **completely unambiguous** and doesn't need a @context -- see how everything is expressed purely in terms of keywords, literals, and iris? no shorthands are used, so you know exactly what each term represents.

note that some nodes have an `@id` and some have a `@value`. if you see a value then that is just a raw value (often just a string, but could be a boolean or a number). if you see an id then that is a link to some other document or resource.

### compaction

compaction is similar to expansion but it keeps values collapsed. so again, you end up with full iris, but you don't have to deal with keywords everywhere other than @id (which sticks around bc any iri represents a node and not just a value)

note that you can compact against a blank @context or supply your own @context.

if you compact against a blank @context, you'll get everything as a full iri:

```json
{
  "@id": "https://social.example/~alice/notes/1",
  "@type": "https://www.w3.org/ns/activitystreams#Note",
  "https://www.w3.org/ns/activitystreams#content": "#HelloWorld :blobcatHug:",
  "https://www.w3.org/ns/activitystreams#tag": [
    {
      "@type": "https://www.w3.org/ns/activitystreams#Hashtag",
      "https://www.w3.org/ns/activitystreams#href": {
        "@id": "https://social.example/tags/HelloWorld"
      },
      "https://www.w3.org/ns/activitystreams#name": "#HelloWorld"
    },
    {
      "@type": "https://joinmastodon.org/ns#Emoji",
      "https://www.w3.org/ns/activitystreams#icon": {
        "@type": "https://www.w3.org/ns/activitystreams#Image",
        "https://www.w3.org/ns/activitystreams#mediaType": "image/png",
        "https://www.w3.org/ns/activitystreams#url": {
          "@id": "https://social.example/emojos/blobcatHug.png"
        }
      },
      "https://www.w3.org/ns/activitystreams#name": "blobcatHug"
    }
  ]
}
```

but if you compact against whatever @context you understand, you will end up with shorthands for everything you understand and leave the full iris only for the stuff you *don't* understand. so again assuming we only understand the activitystreams context:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "id": "https://social.example/~alice/notes/1",
  "type": "Note",
  "content": "#HelloWorld :blobcatHug:",
  "tag": [
    {
      "type": "as:Hashtag",
      "href": "https://social.example/tags/HelloWorld",
      "name": "#HelloWorld"
    },
    {
      "type": "https://joinmastodon.org/ns#Emoji",
      "icon": {
        "type": "Image",
        "mediaType": "image/png",
        "url": "https://social.example/emojos/blobcatHug.png"
      },
      "name": "blobcatHug"
    }
  ]
}
```

note that this basically matches the input, **except** because we didn't compact against the extension vocab terms, we have a few namespaced terms to grapple with:

- `as:Hashtag` is left in there because it's not actually part of the activitystreams namespace. it was a proposed extension, but the context document was never updated to include it. this is why pretty much all existing impls currently include it as an extension term in their supplied @context.
  - note the `as:` namespace was maintained here because `as:` is defined within the activitystreams context document as a convenient shorthand for `https://www.w3.org/ns/activitystreams#` as discussed in [above sections](#namespacing)
- `https://joinmastodon.org/ns#Emoji` was left completely uncompacted because we didn't pass in the joinmastodon namespace at all when compacting
- note that `as:id` is just an alias for json-ld's @id keyword, and `as:type` is likewise just an alias for json-ld's @type keyword

in summary, compaction is good for **simplifying the document based on what you know** -- worst case scenario, you have no @context and you can just parse for full iris (in a manner somewhat more efficient than parsing the expanded form). best case scenario, you have a @context that lets you parse for shorthand terms of your own choosing, and everything else is shortened only as much as possible (so you can generally ignore anything you don't understand, right?)

### flattening

similar to compaction, except you can flatten the document into a single @graph which contains a flat list of nodes.

flattening against no @context:

```json
{
  "@graph": [
    {
      "@id": "_:b0",
      "@type": "https://www.w3.org/ns/activitystreams#Hashtag",
      "https://www.w3.org/ns/activitystreams#href": {
        "@id": "https://social.example/tags/HelloWorld"
      },
      "https://www.w3.org/ns/activitystreams#name": "#HelloWorld"
    },
    {
      "@id": "_:b1",
      "@type": "https://joinmastodon.org/ns#Emoji",
      "https://www.w3.org/ns/activitystreams#icon": {
        "@id": "_:b2"
      },
      "https://www.w3.org/ns/activitystreams#name": "blobcatHug"
    },
    {
      "@id": "_:b2",
      "@type": "https://www.w3.org/ns/activitystreams#Image",
      "https://www.w3.org/ns/activitystreams#mediaType": "image/png",
      "https://www.w3.org/ns/activitystreams#url": {
        "@id": "https://social.example/emojos/blobcatHug.png"
      }
    },
    {
      "@id": "https://social.example/~alice/notes/1",
      "@type": "https://www.w3.org/ns/activitystreams#Note",
      "https://www.w3.org/ns/activitystreams#content": "#HelloWorld :blobcatHug:",
      "https://www.w3.org/ns/activitystreams#tag": [
        {
          "@id": "_:b0"
        },
        {
          "@id": "_:b1"
        }
      ]
    }
  ]
}
```

flattening against our understood @context of only activitystreams:

```json
{
  "@context": "https://www.w3.org/ns/activitystreams",
  "@graph": [
    {
      "id": "_:b0",
      "type": "as:Hashtag",
      "href": "https://social.example/tags/HelloWorld",
      "name": "#HelloWorld"
    },
    {
      "id": "_:b1",
      "type": "https://joinmastodon.org/ns#Emoji",
      "icon": "_:b2",
      "name": "blobcatHug"
    },
    {
      "id": "_:b2",
      "type": "Image",
      "mediaType": "image/png",
      "url": "https://social.example/emojos/blobcatHug.png"
    },
    {
      "id": "https://social.example/~alice/notes/1",
      "type": "Note",
      "content": "#HelloWorld :blobcatHug:",
      "tag": [
        "_:b0",
        "_:b1"
      ]
    }
  ]
}
```

notice that the nodes that didn't have an id previously have now been assigned what is called a *blank node identifier* -- essentially an @id without a namespace and with a randomly generated identifier.

the idea is that you can **parse the @graph and then filter that list by @id to obtain any relevant node** -- no worrying about nesting or otherwise having to deal with structure. for example, we receive `https://social.example/~alice/notes/1` and flatten it against our @context to get a list of nodes, which we filter for the given @id `https://social.example/~alice/notes/1`, and we can see that this node refers to `_:b0` and `_:b1` within the tag property

## conclusion

that's pretty much it tbh

NOTE: activitystreams-core requires that for any serialized as2 document,

- you MUST compact against the activitystreams @context
- you MAY compact against additional contexts
- you MUST NOT override the activitystreams context

additionally if you encounter a document with `application/activity+json` mime-type then you MUST assume the activitystreams context if it is missing

## further reading

[JSON-LD 1.1 -- § 3.2 IRIs](https://www.w3.org/TR/json-ld11/#iris)

> An IRI is defined in [RFC3987] as containing a scheme along with path and optional query and fragment segments. A relative IRI reference is an IRI that is relative to some other IRI. In JSON-LD, with exceptions that are as described below, all relative IRI references are resolved relative to the base IRI.

> While it is a good practice for resource identifiers to be dereferenceable, sometimes this is not practical. In particular, note the [URN] scheme for Uniform Resource Names, such as UUID. An example UUID is urn:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf6.

[JSON-LD 1.1 -- § 3.1 The Context](https://www.w3.org/TR/json-ld11/#the-context)

> Simply speaking, a <mark>context is used to map terms to IRIs</mark>. Terms are case sensitive and most valid strings that are not reserved JSON-LD keywords can be used as a term. Exceptions are the empty string "" and strings that have the form of a keyword (i.e., starting with "@"

> Contexts can either be directly embedded into the document (an embedded context) or be referenced using a URL. Assuming the context document in the previous example can be retrieved at https://json-ld.org/contexts/person.jsonld, it can be referenced by adding a single line and <mark>allows a JSON-LD document to be expressed much more concisely</mark>

[JSON-LD 1.1 -- § 5.1 Expanded Document Form](https://www.w3.org/TR/json-ld11/#expanded-document-form)

> Expansion is the process of taking a JSON-LD document and applying a context such that all IRIs, types, and values are expanded so that the <mark>@context is no longer necessary</mark>.

[JSON-LD 1.1 -- § 5.2 Compacted Document Form](https://www.w3.org/TR/json-ld11/#compacted-document-form)

> Compaction is the process of applying a developer-supplied context to shorten IRIs to terms or compact IRIs and JSON-LD values expressed in expanded form to simple values such as strings or numbers. Often this makes it simpler to work with document as the <mark>data is expressed in application-specific terms</mark>. Compacted documents are also typically easier to read for humans.

> The purpose of compaction is to apply the term definitions, vocabulary mapping, default language, and base IRI to an existing JSON-LD document to cause it to be represented in a form that is tailored to the use of the JSON-LD document <mark>directly as JSON</mark>.

[JSON-LD 1.1 -- § 5.3 Flattened Document Form](https://www.w3.org/TR/json-ld11/#flattened-document-form)

> Flattening collects all properties of a node in a single map and <mark>labels all blank nodes</mark> with blank node identifiers. This ensures a <mark>shape of the data</mark> and consequently may drastically <mark>simplify the code required to process JSON-LD</mark> in certain applications.