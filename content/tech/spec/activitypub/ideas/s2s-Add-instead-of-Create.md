# federating a raw Create is not the same as properly managing a profile

consider an unlisted youtube video, which doesn't appear in the uploads tab. how would you do the same on fedi? possibly by inventing some extension property that hides objects from "profiles"? but the real problem is that there actually isn't a profile at all -- the recipient is expected to keep track of and reconstruct your "profile" based on all known objects!

instead: have an explicit profile Collection. Add stuff into it. when we federate social data we should explicitly be managing the expected presentation of that social data, in as generic a way as possible.

related problem: constructing conversations based on a chain of `inReplyTo` instead of having an explicit collection (likely being used as `context`)