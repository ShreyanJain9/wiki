# What's wrong with SNS

Too many assumptions, too much collapsing and subsuming of things that should be separate.

For example, an account, a user, and a profile are all treated as the same thing when they are not. A status is assumed to only ever be attached to one person. The URLs for all of this all depend on each other.

No. Bad.

Here's what to do instead:

- account = set of credentials used for logging in. may be email and password, 2FA, TOTP, whatever.
- profile = an identity that hosts content and can be followed.
- user = an account that has access to a profile

some examples of decent identifiers:

- /object/id -- just keep it simple. put all objects in the same namespace. the purest form of object storage.
- /p/id and /u/id -- if you plan to only deal with users and posts, sure.
  - user id SHOULD NOT be the username. it should be dynamically generated so that username can be changed later.