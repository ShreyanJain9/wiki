+++
toc = true
autonumbering = true
+++

## (closeable)

### (duplicates)

### (maybe closeable?)

- #813 split docker-compose deployment into "web" and "streaming" [i.e. separate image for streaming API -- is this relevant to current docker strategy?]
- #1298 add custom entries to Getting Started [the motivation was adding a link to code of conduct, although this could be more generally useful outside that context]
- #1306 timeline scroll jumps when new posts come in [addressed by slow mode?]
  - #1774 timeline scroll jumps when posts are deleted
- #3283 composing a long post with overflow will cause jumping on typing
- #3369 ctrl+z after posting used to restore the text to the compose box, but now it does not
- #3858 allow pinning favourites column and user profile columns [half-fixed -- you can pin favs but you still can't pin user profiles]
- #4701 show private posts in hashtag search if you are authorized to see them [closed as "intentional" in #2440 but idk maybe it could be reconsidered]
- #4869 filters should apply to emoji [this was filed back when regex filters were a thing, and seems to have been shown to work even back then, so was not reproducable] [small caveat: could extract a feature request out of this for allowing filtering an emoji by its shortcode? but probably not needed]
- #5403 reblog `url` is sometimes null when it marked non-nullable [i think this isn't happening anymore]
- #5737 store metadata about relationship changes (when/why) [we have profile notes now so that probably works]
- #5896 allow mods to force hide all media from an account [is this the same as "force-mark as sensitive" or is it more like "reject media"?]
- #6033 postgres 10 has a long-running query on public timelines? [is this still relevant]
- #6064 remote suspensions do not always reach you for followers? [has this been fixed now? issue is from 2017]
- #6073 hashtag cloud on landing/about page [is this addressed by trending tags on explore page?]
- #6250 don't send email notifications if a notification is seen in an active session [isn't this done by default now? there's a setting to "always send"]
- #6321 "quiet hours" mode where notifications are not shown [this is done at the OS level]

---

## (uncategorized)

- #482 [retry limit for failed authentication]
- #1910 `/#hashtag` is not parsed as a hashtag (also `)#hashtag` and `_#test`) [because it could be a url fragment]
- #3166 boost/fav counts out of sync (due to being async)
- #3794 [someone is confused why replies are not fetched]
- #3964 filter out private posts if you can't see the full context

### (needs verification)

- #2650 disabling a notification type will cause that notification to be missed; re-enabling the notification type does not bring back the missing notifications during that period in which it was disabled
- #2777 browser notifications do not correctly handle mixed LTR + RTL
- #2826 drag and drop images from othe browser tab
- #2865 empty favourite notification
- #3040 registering an app and getting a user token might return data of previous user token? calling `/api/v1/accounts/verify_credentials` returned wrong user [they seem to be using the password grant, possibly this is related to that?]
- #3061 on post with multiple GIF attachments, going to next one will cause it to not animate
- #3473 hard refresh needed to see multiple pages of new posts?
- #3638 check if post is deleted when clicking reply button; if it is, then remove the post instead of allowing you to compose a reply
- #3680 support 3gpp media upload for conversion [seems to have started working at some point? or maybe reproduced?]
- #3703 if a mention cannot be processed, the whole job fails. maybe it shouldn't?
- #3742 opening a timeline with videos in it can be slow
- #3809 long-press to boost without confirmation (when confirmation modal is enabled for boosting)
- #3930 wrong import order for SCSS [maybe closeable? idk, needs clarification as well, what triggers this scss and so on]
- #3959 rake task `media:remove_remote` should not apply to posts that have been boosted locally
- #3996 sidekiq deadlocks when `:concurrency` > 1
- #4118 repeatedly faving/unfaving will generate a ton of notifications
- #4236 Detect if desktop notifications are supported (hide settings for them if not)
- #4434 nonbreaking spaces in config/locales are not preserved when rendering HTML [where is this relevant?]
- #4573 some posts show up on profiles but not on home timeline [possibly related to #2614]
- #4755 more gender neutral language in i18n german translation
- #4809 browser notification for favourites does not show text of favourited post if an image is attached. but it does for mentions. so that should be consistent
- #4838 settings routes / uris are inconsistent with the parts that they link to
- #4887 Accept-Language header only supports en, not en-US
- #5468 unicode directional formatting sometimes messes with url linking, causing broken links
- #5476 importing following.csv sometimes leads to stuck follow requests for unlocked accounts (workaround: a manual unfollow/refollow fixes it)
- #5527 2fa code should show keypad 0-9 on mobile devices
- #5528 shortcode gets converted to emoji even without breaking whitespace
- #5583 skin color emoji sequence not getting emojified
- #5646 2-frame gifs do not respect delay on final frame
- #5658 remote gif avatar gets corrupted on docker instances
- #5707 wrong localization for zh-TW on relative short timestamp
- #5960 status creation fails on id collision in transaction
- #6070 discovering a post that wasn't federated can cause the counter to +1 mistakenly
- #6083 a certain video got stuck on the first frame
- #6113 favorite/boost counts take a while to update/refresh
- #6139 old accounts deleted manually should be cleaned up [do we have this as a rake task or tootctl cleanup command?]
- #6156 hide boosts from someone you follow, and you will not receive boost notifications from them
- #6208 if the timeline is full of filtered posts, it doesn't load more
- #6329 atom feeds have a CORS issue
- #6338 zwsp is wrongly included in URL parser but correctly removed by hashtag parser
- #6426 audit log breaks when statuses are deleted
- #6432 custom emojis with long shortcodes can break the emoji list in /admin/custom_emojis
- #6554 preview opengraph thumbnail for instance
- #6681 [can't change email more than once?]
- #6923 admin view of statuses doesn't show privacy scope [idk if this was ever fixed in a redesign]
- #6938 welcome email has poorly styled username@domain handle
- #6991 Net::LDAP simple method not supported
- #7025 german translation of 2fa challenge is unclear
- #7145 overscroll? [idk what this means]
- #7170 allow resolving a report "with note" even when no note is included
- #7174 bad ssl on websockets causes entire application to not render

### (feature requests)

- #34 backfill statuses on follow
- #116 communal blocklists
- #139 groups
- #208 custom nicknames
- #281 geotagging posts
- #309 quote + boost
- #512 stories [really more like twitter moments? or generic collections]
- #861 local-only posting
- #928 cryptographic verification of posts (specifically GPG signatures? it should be user-facing like github)
- #979 timelines/columns for selected instances [bubble timeline?]
  - #3812 custom timeline with user-defined domains (also apply filters to public timelines)
  - #3917 let admins define additional timelines including certain domains
- #983 custom audiences / arbitrary addressing (specifically, decide which servers to send your post to)
  - #7135 mutuals-only posts
- #1133 Drafts
- #1162 Insert picture from URL [assuming this means download and reupload? although it would be more interesting to allow remote storage and simply embed it / use the remote url directly...]
- #1208 post to category / topic [like google+ collections]
- #1307 mods should be able to edit posts to add a cw
- #1353 option to show replies in public timelines
- #1405 Local account delegation [i.e. posting on behalf of another account? like tweetdeck team accounts?]
- #1539 mark posts as read (then clear the column)
- #1593 temporary block (expiring blocks just like expiring mutes)
- #1608 show timestamp of when post was favourited (in favourites column and in API) [same for bookmarks?]
- #1685 show status of filed reports or notify them when a report is actioned
- #1800 browse or show posts within a certain date range
- #2089 "Daily Digest" feature that shows only a selection or highlight of posts from certain users (instead of overloading your home timeline)
  - #4451 Discovery / Catch-up timeline that shows posts you may have missed, or perhaps most boosted/faved, etc
- #2317 keep track of media attachments and allow re-using them in new posts [like misskey drive?]
- #3265 attach images to reports (in case users delete their statuses or set abusive avatars?)
- #3546 language filters should use a dropdown instead of checkboxes? should also support more languages
- #3664 Delete all your posts (or some subsets of your data) without deleting your entire account
- #3890 users should be able to override silences [perhaps like disabling twitter's "quality filter"?]
- #3945 mark undelivered mentions / provide feedback when posts are not delivered
- #4106 Support emoji reacts (reactions) on posts [keywords: EmojiReact, EmojiReaction]
- #4207 allow admins to set allowed languages in public timeline for logged-out users
  - #5691 allow users to select their language on the landing page while logged out
- #4983 [original issue was about making posts unboostable, but was misunderstood to be about filtering out notifications for when a certain post is boosted]
- #5787 separate preference for "autoplay media" and "animate emoji"
- #6094 embed profiles or timelines
- #6122 tell the user when they are impacted by instance domain blocks
- #6332 show which local users follow a remote user in the moderation UI
- #6353 admin-configured limits on invites
- #6379 saved searches
- #6387 admin panel diagnostics page
- #6403 option to not convert GIF to video
- #6603 `media_only` on home timeline
- #6635 domain mute without hiding notifications
- #6912 downscale images on upload if they exceed the limit
- #6942 follow local public timelines from other instances [conceptually should be handled by groups instead?]
- #6958 tag avatar and/or header as sensitive/nsfw
- #6982 add accounts to lists without following them [bad idea imo]
- #7042 "View profile as" someone else
- #7066 "Force-unlisted" option for moderating profiles
- #7132 "Bunker mode" to make an account look deleted when it is really not [or possibly just hide the public profile?]
- #7169 upload subtitles with videos

#### (more niche)

- #547 "secure evidence" when admins ban users (instead of wiping all data)
- #4108 Notify me when someone I interact with changes their profile picture
- #5239 Licensing and attribution for custom emoji
- #5444 "emergency instructions" for when a user dies so they can tell admins what to do with their account
- #5657 option to show acct above your display name instead of below it
- #5675 choose the default tab for your profile
- #5714 maybe your posts should be cached offline in the web app's local storage so that if your instance goes offline you have a backup
- #5867 Support 360 panaroma photos
- #6067 notify when a hashtag is used
- #6084 user analytics
- #6090 RSS feed for all interactions on a post?
- #6350 choose the date format localization for timestamps
- #6569 SVG avatar support
- #6726 activity graph and/or calendar for browsing profiles' post history
- #6920 write your own translation of other people's posts
- #7014 add CSS classes based on hashtags, so admins can style certain tagged posts differently from others
- #7082 admin-configured hints for avatar/header image policies
- #7201 show which lists an account is in (from the timeline)

### (meta)

#### (clearly actionable)

- #1404 OpenAPI documentation [but how to generate it?]
- #1487 show out-of-date warning for old servers [how can you tell?]
- #2222 require comments/reason on reports [worth mentioning we have categories now which seems partially relevant]
- #2668 let users point DNS at existing instance, allow multi-tenancy with custom domains
- #3618 reply filtering should hide posts starting with @mentions (even if it's first post in the thread) [basically mirroring twitter and the `.@reply` notation that was necessary in the past]
- #3785 silenced users should be able to DM admins and mods
- #4883 "embed" feature should support informed consent and opt-in to having your posts embedded
- #5781 refetch avatars (and other media?) after remove instance domain block with "reject media" checked
- #5797 prevent replying to someone without mentioning them
- #5883 allow pagination from oldest to newest [or discover the oldest post id somehow]
- #6136 API request limit should be higher -- 100 statuses?
- #6412 external "share" service at share.joinmastodon.org that redirects to your existing instance
- #6474 disable storage of IP addresses
- #6812 rename "local" timeline to something that makes it clear it is not geographically local
- #6828 separate "pinned posts" into separate tab on profiles
  - #19657 combine pinned/featured {posts, hashtags, profiles} into one tab on profiles
- #6884 media usage numbers should be publicly shown? [or possibly to logged-in users, i would assume?]
- #6895 domain blocks with "reject media" should be explained clearly to users, because it looks like a bug currently
- #6899 a way to obtain media attachment cdn links again
- #6970 sensitive content toggle should be per-timeline instead of all-or-nothing. opting into sensitive content from followed users is not the same as opting into sensitive content from *everyone*
- #6976 allow logging in with username instead of email
- #7122 reason should be required on reports
- #7127 notify users when they are affected by moderation actions or decisions
- #10743 author and show hashtags outside of `content`
  - #7250 [alternatively to above:] hide hashtags at the end of a post's `content` [though this can change meaning]
- #7274 show "post unavailable" placeholders whenever a status exists but is not visible (due to privacy or being deleted)

#### (vague or inactionable)

- #967 unique visual identity for niche instances (better customization or theming)
- #1049 better handling for unicode urls (punycode, idn, and so on) as well as schemes/protocols (steam: specifically?) and other encoding issues
- #3782 alternative sorting and aggregation algorithms for timelines (for example, epochs weighted by log(likes) + followers boosting)
- #3796 account aliases (particularly webfinger redirect?)
- #3819 better DM system
- #4870 new user experience
- #5380 make financial supporters visible [manually? integration with patreon/etc?]
- #5515 interest separation + account grouping [seems to be a split between "categories" and "unified timeline"?]
- #5553 urls should not be assumed 23 characters
- #5686 two stages of following -- unapproved and approved. send public posts to unapproved followers [could be handled with audiences instead?]
- #5723 granular post privacy (control whether post goes to public timelines, is available unauthenticated, local-only, etc)
- #5774 ability to change your instance domain (LOCAL_DOMAIN)
- #6192 scss stylesheets should have more color variables
- #6892 reserve usage of `#admin` tag for users who are actually admins [i don't think this is feasible, but if it were, you might want to have it be variable and admin-defined? but remote users exist, so...]
- #6945 disable DMs on an instance level [this makes no sense as-is, unless you have a "public only" mode that rejects anything not addressed to public?]
- #6957 default to not sending sensitive field when adding a status [idk what this means]
- #6992 rich preview cards don't have enough options? [idk what this issue is really about]
- #7114 admins/mods should be able to have a second email for moderation notifications
- #7155 temporarily cache following/follower lists
- #7164 `noindex` should apply to statuses even when boosted [but there's no way to apply it to only part of a page...]
- #7254 some kind of opt-in or out of "bot interactions" [this needs to be more meaningful imo]

---

## Single-category issues

### Admin UI

- #146 admin ui for manually creating user accounts

---

### Backend

- #701 assume http? for bare domains [link `example.com` to `http://example.com`]
- #981 import posts (from csv, specifically?) [but could be from activitypub archive]
- #1781 customizable character limit / status max length (instead of hardcoding 500)
- #2478 allow setting multiple contact/representative accounts for your instance
- #3139 keep track of which users and domains are being widely blocked by local users
- #3811 automatically rake/prune inactive accounts and delete them
- #4296 greylist federation. instances are silenced by default until approved by the admin.
- #5972 Rename "always mark media as sensitive" to "Mark media as sensitive by default" [TODO: Low hanging fruit]
- #6007 button to generate archive exports on behalf of the user, then email them with a link to that archive
- #6031 Full-width japanese period gets included as part of path query in URLs (after the slash). this can break links
- #6351 URL renderer / linkifier adds empty span.invisible to the end of many links
- #6701 URL ending with period does not linkify the period, leading to a broken link
- #6825 empty status with CW should not convert CW to status
- #7056 automatically rake/prune old account headers

#### specific to media

- #5204 indicate the file size on failed uploads so that you can better tell why something went wrong
- #5619 Suport Alibaba Object Storage as an alternative to S3
- #5729 Replace Paperclip with Shrine
- #6352 invalid PNG upload returns 500 Server Error instead of 400 Bad Request
- #6377 hfr video (240fps) transcoded to normal video (60fps), maybe there should be an option

#### specific to search

- #6287 search for multiple hashtags

#### specific to signups and new accounts

- #877 captcha, honeypot, ip limit, and other antispam measures for registering new accounts
- #951 max capacity for registered users (block registration when cap is reached)
- #2045 autogenerate default avatars per-user (specifically foxes?)
- #5647 add warning to registration form that username cannot be changed

#### ActivityPub

- #4640 activitypub property to signal when you opt out of search indexing (similar to robots.txt or robots meta tag in html)
- #4964 use a versioned json-ld @context [currently mastodon just uses the unversioned activitystreams context, but i'm not entirely sure what issues this may cause if any and why]
- #5500 human-readable errors on trying to post to activitypub inbox endpoint
- #6262 converted statuses should append Hashtags to the end of the status_content maybe?
- #6849 deletes are not retried after failure?
- #6878 do not fetch only the first `attributedTo`

#### REST API

- #2048 support Android Intent URIs when registering a client app [currently failing due to use of `#` in uri; workaround is to register custom scheme in android?]
- #5273 API response should include Relationship on each Notification, so that you can tell when a notification came from a follower/following/mutual [arguably you could embed the Relationship on the Account but that might be expensive]
- #5492 API for a client app to update its own registered information (such as `redirect_uris`) using an app token generated from the stored `client_id`+`client_secret`
- #6040 errors should have a non-localized string key to identify which specific error occurred (can be matched exactly)
- #6360 get only ids of accounts from followers/following?
- #6549 API to get ids of deleted statuses without streaming
- #7020 PATCH update_credentials should be replaced with PATCH settings/profile for more intuitive way of updating profile settings like display name, avatar, header image, etc.
- #7021 PUT api/v1/preferences to save new preferences, not just GET existing ones
- #7108 get all admins/mods

#### Streaming API

- #3049 websocket streaming api sometimes encodes payload sometimes doesn't [unlikely to be fixed, due to being a breaking change]
- #5971 add event for new PreviewCard?

#### Security

- #1149 automatically log you out, add checkbox to "stay logged in"
- #1181 do not set a cookie on logged-out users

#### Standards and spec compliance

- #673 RSS feed for your notifications
- #1384 support receiving Webmentions
- #2234 Support Micropub for authoring posts (as an alternative to `POST /api/v1/statuses`)
- #3165 Support Mastodon as an OAuth consumer (login by oauth to some other service / single sign-on / SSO?) ["Mastodon includes the omniauth gem ... it should be possible to support sign up through any OAuth service"]
- #4569 OAuth authorization code OOB flow should allow using an easier-to-type PIN code instead of a 64 digit hex code. twitter and imgur do this apparently
- #4800 Support Mastodon as an OpenID provider (OpenID Connect / OIDC? RFC 6749? IndieAuth is also mentioned)
- #5227 Support Atom feeds [previously supported only profiles, but even that was removed in favor of RSS only]

---

### Deployment

- #1068 offer a Snap/snapcraft package
- #1118 support Terraform from Hashicorp (or chef, or ansible, or puppet, or salt caps)
- #1328 Scalingo setup form should use human-friendly strings instead of plain AWS region names (e.g. `EU (Frankfurt)` instead of `eu-central-1`)
- #1528 /tmp directory fills up over time
- #3576 offer a Debian package

---

### Filters

- #18955 Revamp filters to cover more use cases
  - #6078 Filters ignore username
  - #6596 filter yourself in home timeline

---

### Lists

- #5938 backfill lists when adding new accounts to them
- #5978 Muting a user does not remove old posts from lists

---

### Profiles

- #609 hidden profiles [require auth? "bunker mode"?]
- #1040 custom backgrounds
- #2295 Forward chronological mode / sorting
- #3205 disable or opt out of having a media tab
- #4823 some languages like persian use different words for countable form and plural form, but only one string is used for both the count and for the "all posts" tab on profiles.

---

### Web app

- #380 multiple account management / account switcher (for tootdeck layout / advanced ui, this implies supporting columns per-account)
- #873 font size for larger text
- #1471 show account preview on hover
- #1955 hash acct/url and pick a distinct color to allow more easily identifying when an account is different or is a homograph and possibly impersonation
- #4647 when you hide media that isn't marked nsfw, it should stay hidden persistently (currently it is reshown on refresh)
- #6028 poor contrast on the CW and privacy toggles in the compose form
- #6034 clarify the difference between "desktop notifications" and "push notifications" toggles
- #6035 notification settings are messy and poorly laid out
- #6210 show when a boost was made, not just the original post timestamp
- #6407 show when there are unresolved reports
- #7860 show confirmation dialog before more things
  - #3702 show confirmation dialog before posting

#### specific to advanced UI

- #2054 switching to advanced web UI should load "getting started" instead of a duplicate home/notifications/compose column [this seems to be done only sometimes?]
- #3771 customize the tab bar in the upper-left corner of advanced ui on desktop layout (currently hardcoded to getting started / local / federated / settings / log out)
- #4305 allow moving context column instead of having it on the far right
- #6440 Pin multiple columns of the same type
- #7017 columns should be drag-and-drop instead of rearranged by left/right buttons
- #7050 add workspaces to advanced ui, choose sets of columns to be visible together

#### specific to authoring a post

- #1174 empty CW in compose form should prompt for CW or use default text "content warning", instead of silently dropping the cw and posting a normal post
- #1765 allow replying inline within a column (like tweetdeck or twitter)
- #1860 Preview your post
  - #4848 specifically, it is unclear which characters will be included / parsed as a hashtag or mention or URL
- #2365 copy hashtags on reply
- #3567 mention the booster when replying to a boost
- #3903 suggest a CW based on keywords in your post
- #5108 add a secondary publish button with a different scope (port from glitch-soc)

#### specific to emoji

- #717 option to use system emoji instead of EmojiOne
- #5592 option to not show emoji

##### specific to authoring emoji

- #1908 disambiguate emoji for country flags (maybe sort flags by continent region or geography?) [nightpool: "this is an upstream issue w/ emoji-data"]
- #5445 prevent suggesting emoji when typing emoticons (for example, typing `:-)` should not suggest `:slightly_smiling_face:` in emoji-mart)
- #5498 emoji shortcodes should inherit preferred skin color from emoji-mart
- #5808 shortcodes should always be converted to emoji, or perhaps only when selected manually

#### specific to media

- #921 `rich` OEmbed (e.g. for soundcloud)
- #1255 allow wider aspect ratios instead of zoom-cropping thumbnail [auto height instead of `object-fit: cover`?]
- #2034 when a modal is open, pause all animations in the background
- #6254 "reduce motion" should disable side-scrolling animation for preview of multiple images
- #6983 better handling of PeerTube embed/preview
- #7031 embed remote videos

##### specific to authoring media

- #3860 basic image editing (cropping, rotating, etc) in the web app before posting
- #6879 focal point is misleading. circled area is not in the center, nor is it fully inside the thumbnail crop. [possibly calculation or rendering of focal point may be bugged?]

#### specific to notifications

- #1483 group similar notifications (consecutive notifications of same type should be collapsed)
- #1868 collapse boost/fav notifications to one line only (and `text-overflow: ellipsis` for the rest) [TODO: low hanging fruit for trwnh/mastomods? or is the current "max height" mod good enough for this?]

#### specific to onboarding

- #2019 relaunch the onboarding tutorial at any time
- #3264 show instance info in onboarding tutorial (so that users are aware of rules/etc)
- #6867 choose your language filters after signup

#### nonspecific

##### accessibility

- #1680 line height should be relative to font size, not absolute [TODO: low hanging fruit?]
- #3660 better contrast and visibility on button for closing image modals
- #3875 "edit profile" link should be easier to tap on mobile [TODO: low hanging fruit]
- #4508 better focus contrast / visibility for status action buttons [currently, there is a slight transparent background on focus but no prominent outline like other focused elements] [TODO: low hanging fruit] [side note: the video player has even worse contrast on focus, i legit could not tell it was focused]
- #4510 when you focus on a post and expand it, the focus should shift to the detailed status view instead of staying in the column
- #6152 larger click/touch target for adding account to list
- #6185 error toast notifications should show up closer to the source of the error, not in the lower left corner

##### performance

- #787 garbage collection for open webUI tabs (react redux / state management)
- #1379 switching columns on mobile should be optimized
- #1469 use SVG icons instead of web fonts
- #5504 components are re-rendered whenever redux state changes, due to `mapStateToProps`
- #6438 memory usage continues to grow as new items are loaded into timelines

##### architecture

- #4478 reload a single column or conversation without reloading the whole tab
- #7113 minimize the compose box on mobile

##### bugs

- #5160 fullscreen video on desktop safari doesn't work right [seemingly last reproduced in june 2022 on macos 12.4 + safari 15.5]

##### unimplemented API features

- #1900 clear one notification [i.e. implement `/api/v1/notifications/:id/dismiss`]
- #6702 role badges

##### staying in-app

- #739 edit profile in webUI instead of settings page
- #946 "back" button in header should not take you out of the web app (you should remain in-app)
- #1399 links to activitypub objects (posts/profiles) should stay in-app [i think this specifically calls out links in profile bios? that might be harder]
  - #2136 links in posts (to other posts) should open those posts in-app
  - #3185 links in bios (to other profiles) should open those profiles in-app
  - #3982 links in bios (to hashtags) should open those hashtags in-app
- #3201 indicate that a language filter is active and allow temporarily activating language filters in-app (without going to settings)

##### meta

- #1249 use an icon for CW instead of literally `CW`
- #1850 Rename "Getting started" to something else (OP suggested "Home" but gargron instead suggests "menu" or "main menu")
- #1856 UI mockups for easier switching of columns?
- #2210 UI mockup for pre-rendering mentions as links, instead of showing the entire address in the compose box
- #3872 combine Getting Started + Compose into one column, since they're both always open?
- #4399 CW toggle and sensitive media toggle should be linked such that expanding a post expands the media with it, and collapsing a post should rehide the media
  - #4689 CW and sensitive media should not be linked at all
  - #6141 show a media indicator in the CW "show more" toggle
- #5727 UI mockup for two-column layout on tablets
- #5736 more layout adjustments. variable column width, variable font size, variable media thumbnail size
- #5958 quickly switch between lists in a pinned column, or allow pinning the main "Lists" list
- #6267 use typescript

---

<script>

let tag = /\#(\d+)/g;
let link = '<a href="https://github.com/mastodon/mastodon/issues/$1">#$1</a>';

document
.querySelectorAll('li')
.forEach(
  (node) => {
    node.innerHTML = node.innerHTML.replace(tag,link);
  }
);

</script>