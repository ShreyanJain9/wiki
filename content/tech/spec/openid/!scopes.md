
<https://mastodon.social/@trwnh/109799253222783029>

diving back into openid specs to wrap my head around how this whole "identity provider" thing might work

i have a webfinger server which is like step 1 for the intended flow -- ask webfinger for an account's link rel oidc issuer, then `GET /.well-known/openid-configuration` to find endpoints and other such info. the only real gap seems to be a major one: scope. there's no standardized definition for scope except for `profile` `email` `phone` and `address` as defined by OIDC-CORE...

this is obviously not good enough for resource control, so it seems i might be firmly in extension territory

i am not an expert here but my naive concept would be to replicate unix acls for directories and subdirectories? you can read this https uri and all children, or you can get write access. but i'd really like to do things in a more ocappy way if feasible