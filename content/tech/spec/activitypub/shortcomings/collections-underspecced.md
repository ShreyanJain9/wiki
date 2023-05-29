# collections are kinda underspecced tbh

loosely inspired by the submission of FEP-5bf0 Collection sorting and filtering

## filtering

you could pre-create collections that contain a subset of items, but there's no way to dynamically do server-side filtering; this has to be done client-side by downloading the entire collection and creating any useful indices

## sorting

OrderedCollection is mandated reverse-chronological but it should have been possible to specify the ordering

taking schema.org/ItemList and schema.org/ListItem as prior art:

- `ListItem.position` is the index
- `ItemList.itemListOrder` is the type of ordering
	- `itemListOrderAscending`
	- `itemListOrderDescending`
	- `itemListUnordered`

what might this imply is needed for activitypub?

- `orderedBy`?
	- what about multiple ordering criteria? SQL has `ORDER BY`
	- actually, is a query language a good fit here?
- `reversed`? more like `order: Ascending` or `order: Descending`?

## querying

how do you tell if an arbitrary object is included in a collection or not? without iterating over the entire collection, that is.