https://mastodon.social/@trwnh/110310774875374491

re: mastodon and matrix having "default servers" that end up being too big: i feel like if they were easier to selfhost it would be less of a problem. the "default server" paradigm is more effective when you can't easily set up other servers.

you ever try to host matrix? it's awful. like 2GB memory usage just for a single user in a big room.

mastodon is getting to be pretty similar, not because of the base software per se, but because of how many moving parts it has and how much resource usage accrues as a result. rails, postgres, redis, optional elasticsearch if you want working search, a nodejs streaming server and frontend... that's before you even get to the network traffic due to architectural decisions

sure, but you can at least block entire bad servers with little-to-no collateral damage. and you can also use allowlist federation or some other restriction mechanism to avoid fully open federation. just like avoiding fully open registrations.

i've oft thought about an opt-in federation of instances that require human approval for registrations. or a closed "federation" in the irc distributed sense.

it'll never happen because gargron wants more users, and approval-based signup makes a lot of disinterested people bounce before actually trying the software. but it's worth considering imo at least as a thought experiment