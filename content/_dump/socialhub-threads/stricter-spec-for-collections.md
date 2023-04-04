+++
title = "Stricter specifications for pagination of Collections and OrderedCollections"
date = "2022-11-05"
+++

## Preserved text

<https://socialhub.activitypub.rocks/t/stricter-specifications-for-pagination-of-collections-and-orderedcollections/2633>

@trwnh:

<article>

## Overview

ActivityPub primarily depends on direct delivery of activities, but many implementations expose old post history via the `outbox`. In theory, you could fetch the outbox to discover old posts that weren’t delivered to you but should still be visible to you (e.g. Public posts that persist in the outbox). However, there is one big problem: pagination.

Specifically, pagination is an issue because you will have to fetch multiple pages, and you don’t know exactly when to stop, or how to discover gaps. You may be able to fetch up until you see a post that you already have, but there may be other unseen posts beyond that one. The only way to be sure is to fetch every single page of the outbox, which can be a costly operation.

## Recommendations

Arguably, this situation can be improved by making some specific recommendations:

### Construct chronologically, but present in reverse order

Because an OrderedCollection is mandated to be “reverse chronological” specifically, extra care needs to be taken to allow stable page references. Perhaps pages should be built chronologically and simply presented in reverse order, crucially with the `first` page containing less than than the max page size.

Example: A page size of 10 and a collection of 23 items should be split into pages of 3, 10, 10. These pages would be presented as such:

```
[
[23 22 21]
[20 19 18 17 16 15 14 13 12 11]
[10 9 8 7 6 5 4 3 2 1]
]
```

### Stable page references should also be reversed

Furthermore, in order to maintain stable page references, such that if you’ve fetched a page before you don’t have to fetch it again, page counters should be assigned in reverse order as well.

Taking the example from above, the pages would be identified as 3, 2, 1:

```
[
[23 22 21] // page 3
[20 19 18 17 16 15 14 13 12 11] // page 2
[10 9 8 7 6 5 4 3 2 1] // page 1
]
```

### Deleted items should either be Tombstoned or change the page size

All this work is useless if the pages have to be recalculated or the items get shifted to a different page. To prevent this, either serve a Tombstone in place of deleted items, or otherwise freeze the upper and lower bound of a page while allowing variable page sizes.

For example, let’s say we delete post 17. The result might look like this:

```
[
[23 22 21] // page 3 (3 items)
[20 19 18 T 16 15 14 13 12 11] // page 2 (10 items, 1 Tombstone)
[10 9 8 7 6 5 4 3 2 1] // page 1 (10 items)
]
```

Or, it might look like this:

```
[
[23 22 21] // page 3 (3 items)
[20 19 18 16 15 14 12 11] // page 2 (9 items)
[10 9 8 6 5 4 3 2 1] // page 1 (10 items)
]
```

Notice that page 3 remains unchanged, rather than item 21 becoming part of the 2nd page.

### Accessing pages should be done in a consistent way

The final piece of the puzzle is a way to consistently load specific pages. For example, consider a collection at `/collection/id`. You might be able to attach a query parameter `?page=N` to access the Nth page via `/collection/id?page=N`. Or you might have some route such as `/collection/id/page/N`. Whatever the case, there should be a way of getting pages that can be expected to work across all implementations. Or, at the very least, a way that may be inferred easily, but a standard pagination technique would be better.

My thinking is that `/page/N` would be better, because it would allow for static pages as an option more easily,

Also for consistency: Tombstone is preferable to exclusion, because it allows dynamic page sizing on-the-fly in dynamic servers that use query parameters.

</article>

---

@silverpill:

Perhaps a timestamp filter could solve this problem?
For example, `/collection/id?after=1667766000` can return a paginated subset of the collection, and if the client knows the time of the last sync, it can retrieve missing objects with fewer requests.
This way implementations can continue to use their preferred pagination mechanism.

---

@trwnh:

Having a timestamp filter might work for dynamic server implementations but not for static server implementations. I took care in making sure, while writing the above post, that the recommendations would be applicable to both static and dynamic servers.

---

@trwnh:

in writing this up i seem to have overlooked the `startIndex` property of OrderedCollectionPage, which is basically just a positive offset for how far into the OrderedCollection you are with the first item. unfortunately this is less useful due to reverse chronology being mandated by ActivityPub; it would be far more useful in a forward-chronological collection. it also doesn’t apply to regular CollectionPage sadly.

there is still the option of having a regular Collection contain OrderedCollectionPage, though, or otherwise simply disregarding the “MUST be reverse chronological” bit and committing a spec violation.

---

## current thoughts

idk. really wish the OrderedCollection wasn't mandated to be reverse-chron and it was instead forward-chron at least. this carries over to OrderedCollectionPage too...