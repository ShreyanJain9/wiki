+++
+++

A Collection is an Object that has `items` or `orderedItems`

however, `orderedItems` is valid on both Collection and OrderedCollection! it is defined like this in the as2 context:

```json
"orderedItems": {
	"@id": "as:items",
	"@type": "@id",
	"@container": "@list"
}
```

so it is basically just an alias for `items` where it MUST be an array, and the array's order matters. (if it were `@container: set`, then it MUST be an array, but the array's order does not matter.)

### tangent: a Collection may be ordered without being an OrderedCollection

OrderedCollection is defined as strictly reverse chronological by ActivityPub. however, other orderings are valid on regular Collections. the use of the `orderedItems` term allows plain-JSON implementations to do exactly this.