# What's wrong with address books

Addresses are not owned. They are leased.

Compare to physical street addresses. People move houses all the time, so their address changes. In the same way, people change digital addresses like phone numbers. This makes it hell to reconstruct a conversation later based on your current contact listings. Do you keep that old number attached to that contact forever, even if you know they're not using it anymore? That's awkward because you wouldnt message that number anymore, it'd be a stranger.

The real issue is reuse of addresses. We don't really have to worry about date ranges of email addresses because email providers generally don't reassign usernames. But it might be nice to still be able to mark an email as implicitly "inactive". So what do we do about all this?

Assign date ranges to contact addressing methods.

Start date, end date. "X was using this phone number between 2011-01-01 and 2015-06-01." For messaging archives you can now reliably dereference a phone number to a contact name, without having to worry about who has the phone number *now*.

- Assign date ranges of when an address was used
- Mark certain method(s) as primary
- Display name is better than forcing people to use first/last. Allow optional markup of name parts, but name should be single text entry field and separate from other metadata like given/family/middle/etc.
- Arbitrary key-value pairs for misc info that isn't necessarily structured. But structure helps wherever possible.

Prior art:
- vcard. UGH.
- Monica. a great improvement but still kinda limited