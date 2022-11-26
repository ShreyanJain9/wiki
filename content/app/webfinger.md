this is really the interface that makes pure AP servers discoverable.

problem: AP `id` can be literally anything
solution: have webfinger return the `id` based on some other "friendly" uri lookup

/.well-known/webfinger?resource=

- username? `acct:trwnh` or for masto compat `?resource=username@domain.com` or `?resource=user%40email.com@domain.com` per rfc for acct uri
- phone number? `tel:+1205578890` -- needs to be standardized/internationalized i guess, maybe also accept `sms:`
- email? `mailto:a@trwnh.com`
- xmpp? `xmpp:a@trwnh.com`
- other -- http? https? urn?