# Open Web

The open web is built on multiple technologies recommended by the W3C. This page is dedicated to taking notes about protocols and standards, and basic details of their implementation.

{{<toc>}}

## Static content

Technologies that can be included in static HTML files, no backend server needed. Static site generators can generate these if the template supports them.

### Microformats

Microformats are used to define the Semantic Web, which aims to make machine-readable meta-content. http://microformats.org

## Dynamic content

### Indieweb

The goal of the Indieweb project is to allow websites to be used as full-fledged substitutes for other services. The ideal indiewebsite would be able to self-host media content in multiple post types, serve as an authentication for logging into other services, and be used as a person's identity. Indieweb strategies include using your domain name as your identity, self-hosting your own data on that domain, syndicating copies of your content to silo services, and maintaining actually permanent permalinks. http://indieweb.org

### ActivityPub

ActivityPub is a federated social networking protocol that defines server-to-server and client-to-server interactions via ActivityStreams 2.0 and the Activity Vocab. It's basically email over JSON. Actors act on Objects that are sent and delivered via an Inbox and Outbox in various Collections. https://www.w3.org/TR/activitypub/