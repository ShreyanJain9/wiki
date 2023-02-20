+++
+++

# notes about uris, urls, urns, and so on

## a general ideological promotion of the Web and HTTP

most importantly the web allows for hyperlinks between web pages/resources. you can extend this to arbitrary data; the argument for "hyperdata" evolved into "linked data"

<https://en.wikipedia.org/wiki/Info_URI_scheme>

> In 2010, the [info:] registry was closed, in view of the increasing importance of the W3C Linked Data paradigm, and adopters of the info scheme were advised to migrate resource identity requirements towards "mainstream Web practices." <https://oclc-research.github.io/infoURI-Frozen/>

you can see this attitude expressed in w3c materials like [URNs, Nameespaces, and Registries](https://www.w3.org/2001/tag/doc/URNsAndRegistries-50):

> This finding addresses the questions "When should URNs or URIs with novel URI schemes be used to name information resources for the Web?" and "Should registries be provided for such identifiers?". The answers given are "Rarely if ever" and "Probably not". Common arguments in favor of such novel naming schemas are examined, and their properties compared with those of the existing http: URI scheme. Three case studies are then presented, illustrating how the http: URI scheme can be used to achieve many of the stated requirements for new URI schemes.

concerns about persistence are waved away by saying "http: URIs support persistence as well as it is in-practice possible to do so." in the w3c TAG's view, persistence is a management issue for "owners and operators". in *my* view, this completely overlooks that there may be *multiple* or *zero* "owners and operators". in other words, **the distributed and decentralized use-cases are unhandled.**

the assumption made by this document is that http: URIs are not protocol dependent (no, really!) because if you never want to retrieve a resource, it is irrelevant that it starts with http:. if you do want to retrieve a resource then you will need to map the identifier to a protocol. coincidentally, the position of the document is that "http: URIs are *not* locations" (no, again, really!). idk about you but i would say that a resource is located on a server with an IP that can be looked up using DNS. but that domain namespace is not authoritatively yours forever -- it is only yours as long as you register and renew that domain.

the ultimate irony imo is that some of the examples given no longer resolve lmao. `http://lccn.info` is cited as a better way of encoding `info:lccn/` but that's a "server not found" now.

### workarounds for persistence in HTTP

for a given domain: use permalinks. design your uris so that they don't break or change. see [Cool URIs don't change](https://www.w3.org/Provider/Style/URI)

> Except insolvency, nothing prevents the domain name owner from keeping the name. And in theory the URI space under your domain name is totally under your control, so you can make it as stable as you like. Pretty much the only good reason for a document to disappear from the Web is that the company which owned the domain name went out of business or can no longer afford to keep the server running. Then why are there so many dangling links in the world? Part of it is just lack of forethought.

for limited domain independence: use persistent URLs (PURL). basically just a proxy that promises to stay up forever and will redirect to the current location of the resource. or, if you don't care about resolution, then it is at the very least a stable namespace. instead of registering with DNS you register with whoever is maintaining the PURL service -- purl.org or w3id.org or whatever. the funny thing is that we've already seen it be challenged; the OCLC stopped maintaining the former in 2016, and it only continues to exist because they agreed to pass it off to the Internet Archive.

if you want persistence even in the case where a (PURL) domain lapses or is claimed by someone else or otherwise goes insolvent... tough luck. there is no way in HTTP to have any sort of domain-independent or cross-domain namespace. your URI is bound to that domain and the domain namespace. best you can do is bind it to some domain you trust to be longer-lived than your own.

### the failure of XRI

<https://en.wikipedia.org/wiki/Extensible_Resource_Identifier>

> The goal of XRI was a standard syntax and discovery format for abstract, structured identifiers that are domain-, location-, application-, and transport-independent, so they can be shared across any number of domains, directories, and interaction protocols.

> [XRI's] failure [was] attributed to the intervention of the W3C Technical Architecture Group which recommended against using XRIs or taking the XRI specifications forward. The core of the dispute is whether the widely interoperable HTTP URIs are capable of fulfilling the role of abstract, structured identifiers, as the TAG believes, but whose limitations the XRI Technical Committee was formed specifically to address.

<https://en.wikipedia.org/wiki/I-name>

> One problem XRIs are designed to solve is persistent addressing — how to maintain an address that does not need to change no matter how often the contact details of a person or organization change. XRIs accomplish this by adding a new layer of abstraction over the existing IP numbering and DNS naming layers used on the Internet today (as well as over other type of addresses, such as phone numbers or instant messaging addresses). Such an abstraction layer is not new — URNs (Uniform Resource Names) and other persistent identifier architectures have the same effect. What's different about the XRI layer is that it offers a single uniform syntax and resolution protocol for two different types of identifiers: I-names and I-numbers.

> What most differentiates i-names from domain names is that in practice they will have a synonymous (equivalent) persistent i-number [...] I-numbers are machine readable identifiers (similar to IP addresses) that are assigned to a resource (for instance, a person, organization, application or file) and never reassigned. This means an i-number can always be used to address a network representation of the resource as long it remains available anywhere on the network. I-numbers, like IP addresses, are designed to be efficient for network routers to process and resolve. 

<https://en.wikipedia.org/wiki/I-number>

> Conceptually, an i-number is similar to an IP address, except i-numbers operate at a much higher level of abstraction in Internet addressing architecture. The other key difference is that i-numbers are persistent, i.e., once they are assigned to a resource, they are never reassigned. By contrast, IP addresses are constantly reassigned, e.g., your computer may have a different IP address every time it connects to the Internet.

> XRIs accomplish this by adding a third layer of abstract addressing over the existing layers: IP numbering (first layer) and DNS naming (second layer). The notion of a third layer for persistent addressing is not new — Uniform Resource Names (URNs) and other persistent identifier architectures have been developed for this purposes. However the XRI layer is the first architecture that combines a uniform syntax and resolution protocol for both persistent and reassignable identifiers.

> different XRIs that all point to the same resource are called synonyms. I-name synonyms make it easy for humans to discover and address the resource, while i-number synonyms make it easy for machines to maintain a persistent identity for the resource. For example, if a company changes its name, it may register a new i-name and sell its old i-name to another company, however its i-number can remain the same — and links to the company that use its i-number won't break.

best adoption it got was being part of openID 2, but that's about it

```text
   DNS : plaintext DNS response
:: XRI resolution : XRD response
:: Webfinger resolution : JRD response
```

## the URL/URN split was put aside in favor of URI and later IRI

<https://en.wikipedia.org/wiki/Uniform_Resource_Name>

> Since RFC 3986 in 2005, use of the terms "Uniform Resource Name" and "Uniform Resource Locator" has been deprecated in technical standards in favor of the term Uniform Resource Identifier (URI), which encompasses both, a view proposed in 2001 by a joint working group between the World Wide Web Consortium (W3C) and Internet Engineering Task Force (IETF).

> In the "contemporary view", as it is called, all URIs identify or name resources, perhaps uniquely and persistently, with some of them also being "locators" which are resolvable in conjunction with a specified protocol to a representation of the resources. [...] Since resources can move, opaque identifiers which are not locators and are not bound to particular locations are arguably more likely than identifiers which are locators to remain unique and persistent over time. But whether a URI is resolvable depends on many operational and practical details, irrespective of whether it is called a "name" or a "locator". In the contemporary view, there is no bright line between "names" and "locators".

## URNs specifically lack resolution, which is why URLs are preferred today

really the biggest argument, the benefit that http-web maximalists keep citing, is the prevalence and wide adoption base for http: URIs. this is because of web browsers. you can load a URL and get a web page or image or whatever other resource in one step.

### ark and "name 2 thing"

<https://en.wikipedia.org/wiki/Archival_Resource_Key>

> In 2019 it was registered as a Uniform Resource Identifier (URI) [...] ARKs can be maintained and resolved locally using open source software such as Noid (Nice Opaque Identifiers) or via services such as EZID. Most implementations are decentralized and no fees are charged for the right to assign ARKs. Some implementations choose to publish ARKs via the centralized N2T (Name-to-Thing) resolver. <http://n2t.net/>

### more generally: URN resolvers

### a note about authority in namespaces

DNS authority means you can assign names underneath a given domain name... this authority derives from your lease of the domain name.

URN authority means you can assign names underneath a given URN namespace... this authority derives from some agreement with standards bodies.

therefore, it can be said that the real difference between DNS and URN is that the former grants authority based on technical registration, while the latter grants authority based on social registration. in other words a URN can be used even without the internet. consequently, **URNs can be handed out freely and in a decentralized manner**.

## see also

- [Persistent Identifiers (Permalink) (Handle, Purl, URN, ARK, XRI…)](https://konubinix.eu/braindump/posts/persistent_identifiers_permalink_handle_purl_urn_ark_xri/)
- [XRI - Internet Identity Commons](https://iiw.idcommons.net/XRI)
	- [An Introduction to XRIs - OASIS Working Draft 04](https://www.oasis-open.org/committees/download.php/11857/xri-intro-V2.0-wd-04.pdf)
	- [XRI 2.0 FAQ](https://www.oasis-open.org/committees/xri/faq.php)
	- [XRI Resolution](http://docs.oasis-open.org/xri/2.0/specs/xri-resolution-V2.0.html)
- [XML Namespace Name: URN or URL?](https://www.xfront.com/URLversusURN.pdf)