+++
+++

# extensibility for plain-json consumers is underconstrained

the recommendation in the spec is to use JSON-LD for extensions, which leaves open the "best practices" on how might one expect to serialize and parse those extensions.

my thoughts:

- if you consider the mastodon protocol/profile, then you basically have to compact against the same context as them, because mastodon only understands shorthand terms that match their own definitions.
- a full IRI is the only unambiguous way to refer to the same thing