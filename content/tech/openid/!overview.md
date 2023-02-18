+++
+++

ok let's dive in i guess

<https://openid.net/developers/specs/>

here's what i could grasp

## spec summary

### openid connect core (OIDC-CORE)

main points:

- use oauth 2 to issue an **ID Token**
- use jwt to construct specific claims
	- the `iss`uer is the url of the IdP host
	- the `sub`ject is some unique identifier (uuid? etc)
	- the `aud`ience MUST contain the `client_id` and MAY be an array with other values
	- the `exp`iry is some unix timestamp (integer seconds since jan 1970)
	- the `iat` (issued at) is also a unix timestamp
	- the `auth_time` is a timestamp of when the user was authenticated
		- required for `max_age` or when `auth_time` is marked as "essential claim"
	- the `nonce` is a string that mitigates replay attacks.
		- clients add this and you pass it back so they can verify
		- required only if the client gives it to you, MUST be passed back as-is with no processing
	- OPTIONAL: the `acr` (authentication class reference) indicates the "level"?? of the authentication. similar to `nist_auth_level` in openid 2.0? 
		- > Parties using this claim will need to agree upon the meanings of the values used, which may be context-specific
		- so this is gonna require more in-depth investigation later
	- OPTIONAL: the `amr` (authentication method reference) is an array of identifiers for which methods were used to authenticate -- password/otp/etc
		- > The definition of particular values to be used in the amr Claim is beyond the scope of this specification
		- so this is also gonna need investigation later
	- OPTIONAL: the `azp` is the authorized party, this MUST contain the `client_id`. only needed when the `aud` is a single value that is *different*, but may be inclued even if it's the same.
	- OPTIONAL: you MAY include other claims other than the standardized ones above, or per other standards
- use jws to sign the token (required) and jwe to encrypt the token (optional)
	- sign, THEN encrypt, to produce a nested jwt as defined in the jwt spec

example: client `s6BhdRkqt3` makes an authentication request `n-0S6_WzA2Mj`, and gets a response that user `24400320` on `https://server.example.com` was authenticated at `1311280970` and this claim expires in 1000 seconds

```json
{
	// the authority
	"iss": "https://server.example.com",
	"sub": "24400320",
	// the request
	"aud": "s6BhdRkqt3",
	"nonce": "n-0S6_WzA2Mj",
	// timestamps and ranges
	"auth_time": 1311280969,
	"iat": 1311280970,
	"exp": 1311281970,
	// some extra info about the authn level
	"acr": "urn:mace:incommon:iap:silver"
}
```

three flows defined as part of oauth 2: `response_type` ==
- `code` for authorization code flow. code is returned from authorization endpoint, exchanged for token from token endpoint [best practice is to use this with PKCE]
- `id_token` or both `id_token token` for implicit flow. allows one round-trip, but inherently insecure [deprecated in oauth 2.1 even]
- hybrid flow? ask for some combo of `code` and at least one of `id_token` or `token`.

also note that `token` is defined in oauth 2 but not? used for oidc? since no IdT is returned

## putting it all together?

per OIDC-DISCOVERY: webfinger? look for or query `rel` link `http://openid.net/specs/connect/1.0/issuer` and you obtain the host

```json
{
	"subject": "acct:joe@example.com", // or the url "https://example.com/joe" could work. more than one way to get to this point, in theory.
	"links":
		[
			{
			"rel": "http://openid.net/specs/connect/1.0/issuer",
			"href": "https://server.example.com" // bingo.
			}
		]
}
```

also per OIDC-DISCOVERY: obtain the openid configuration to get endpoints and relevant info

```http
GET /.well-known/openid-configuration HTTP/1.1
Host: server.example.com

{
	"issuer":
		"https://server.example.com",
	"authorization_endpoint":
		"https://server.example.com/connect/authorize",
	"token_endpoint":
		"https://server.example.com/connect/token",
	"registration_endpoint":
		"https://server.example.com/connect/register",
	// ...
}
```

register your client with Dynamic Client Registration (OIDC-DCR)

```http
POST /connect/register HTTP/1.1
Content-Type: application/json
Accept: application/json
Authorization: Bearer eyJhbGciOiJSUzI1NiJ9.eyJ ...   # may be obtained out-of-band or otherwise dropped if you want anyone to be able to register

{
	// ...
}
```

make a GET request following the authcode flow. scopes defined in OIDC-CORE, along with their associated claims:

- the `openid` scope grants standard claims like `sub` and must be present to validate the auth request
- the `profile` scope grants claims for
	- `name`
	- `family_name`
	- `given_name`
	- `middle_name`
	- `nickname`
	- `preferred_username`
	- `profile`
	- `picture`
	- `website`
	- `gender`
	- `birthdate`
	- `zoneinfo`
	- `locale`
	- `updated_at`
- the `email` scope grants claims for
	- `email`
	- `email_verified`
- the `address` scope grants claims for
	- `address`
- the `phone` scope grants claims for
	- `phone_number`
	- `phone_number_verified`

[TODO: are there any standards for granular scopes or for information not in this list? i see FHIR for health information registries, like `patient/*.* sens/ETH sens/PSY btg` to request access to all patient data with all methods, plus substance abuse and psychiatry data, while "breaking the glass" (the patient is unavailable, so log this in an audit log somewhere). so i'm guessing that i'm going to be firmly in extension territory here...]

also per OIDC-PROMPT-CREATE you may add `prompt=create` to initiate a user registration?

```http
GET /authorize?
    response_type=code
    &scope=openid%20profile%20email
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
```

exchange your code for a user token

```http
POST /token?
     // ...
```

## drafts to consider (and their implications)

per OID4VP (verifiable presentations, related to verifiable credentials): you may have client_id == redirect_uri or you may have iss == aud. this indicates a self-issued token. maybe the value is a DID?