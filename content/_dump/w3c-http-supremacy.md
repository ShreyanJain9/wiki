https://mastodon.social/@trwnh/109886416964217719

reading a lot of old w3 literature and while there's a lot of good stuff in there, it is *infuriating* how they are so blindly in support of using http for everything, including identifiers. they probably just failed to predict the future, but they keep stopping just short of admitting http's biggest problem: when the server goes down, your uri can't be dereferenced anymore!

(specifically i was reading https://www.w3.org/2001/tag/doc/URNsAndRegistries-50.xml which argues against urns because you can't dereference them)

am i being too harsh on an editorial from 2001, sure, yeah, probably no one was seriously thinking of decentralized systems back then. if you just wanna obtain a resource from a given organization or whatever, it is generally a given that the org has its own domain name and can assign ids on that domain. but the whole thing falls apart when you need a global namespace that can be requested from multiple domains. this is *exactly* where urns are useful! like, naming things is in some way id!

if we follow the logic of http maximalists from 2001, then we would not need the isbn or doi namespaces, we could just assign identifiers on an http hostname. a doi can be resolved using an http url so why not just use the url directly?

well, that assumes persistence that simply isn't guaranteed. the doi foundation will tell you itself: https://www.doi.org/the-identifier/resources/factsheets/doi-system-and-persistent-urls

> PURLs are all http and inherit both the strength and weakness of that approach

it's those weaknesses that kept being ignored

