Heads up: there isn't a lot to explain because a lot of the insights about the data breach aren't publicly disclosed, so bear that in mind.

## What happened?

Back in August 2026, a dataset containing more than 7.3 million Chess.com records appeared on a forum called BreachForums. At first, this sounds like a normal data breach where someone hacked into Chess.com and was able to dump a bunch of information from a database.

However... the available evidence suggests something more like automated data harvesting, also commonly referred to as scraping.

The important difference is that there is currently no evidence showing that the person who created the dataset broke into Chess.com's internal database. Instead, it appears that information was collected through Chess.com's web-facing systems over time. 

### Step 1: Start with Existing Email Addresses

According to Chess.com's response to the incident, the person who created the dataset already had a large list of email addresses from another source.

Which means they did not necessarily obtain the email from Chess.com.

This is also supported by HaveIBeenPwned. Of the approximately 4.65 million unique email addresses found in the dataset, HIBP reported that 99% had already appeared in previous data breaches.

So imagine starting with:
```
john@example.com
sarah@example.com
mike@example.com
```
At this point, these are just email addresses. We don't necessarily know whether any of them have Chess.com accounts.

### Step 2: Check the Emails Against Chess.com
Chess.com says its lookup functionality was used to find profile information associated with matching email addresses. This was a functionality that was abused during their 2023 breach as well...

The Basic idea is:
``` Existing email address -> Chess.com lookup -> Does it match a Chess.com account -> YES / NO ```

It's that simple. If there was no match, that email wasn't useful for finding a Chess.com account. 

If there was a match, the email could now be connected to a Chess.com user.

For example:
``` john@example.com -> Chess.com lookup -> ChessPlayer123 (username) ```
That connection is important

Before, someone had an email address.

Great, now they potentially know:
``` john@example.com -> ChessPlayer123 ```

Just to briefly mention, we do not currently know the exact endpoint or request used for this lookup. Chess.com has referred to it as a lookup function, but the sources I reviewed don't document the exact 2026 HTTP request. 

### Step 3: Collect Information about the Account
Once an email had been connected to a Chess.com account, additional information about that account could be collected.


The leaked dataset reportedly contained information such as:
```
email
partial email
username
user ID
UUID
first and last name
country
location
locale
chess title
ratings
premium information
verification/activation information
registration date
last login
```

<img width="948" height="492" alt="image" src="https://github.com/user-attachments/assets/fbc8b11e-819b-44b3-91cb-7f8dcb87a0f7" />

To be quite honest, a lot of this information is already public information that can normally be associated with a Chess.com profile, such as Usernames, ratings, titles, user-provided profile information (location, phone number, email), and friends.

However, the interesting thing is that we do not know exactly how every field in the leaked dataset was obtained.

For example, the public reporting does not tell us exactly which 2026 request returned:
```
UUID
user ID
verification status
activation status
locale
```

**I will very briefly talk about `gam_audiences` and `audiences_member_of` at the end because those are the most interesting...**

### Step 4: Automate the Process

Doing this manually for millions of email addresses would take forever. 

Instead, the evidence suggests the collection process had to be automated.

```Take an email -> Check Chess.com -> Account found? -> Collect available information -> Save it -> Move to next email -> Repeat```

Researchers found that the records appeared to have been collected over nine consecutive days, rather than all at once.

Interestingly enough, they also found duplicate user IDs, meaning some Chess.com accounts appeared to have been collected more than once.

This pattern is one reason researchers believe the dataset was created through automated harvesting rather than someone simply downloading a Chess.com database.

### Why Are There 7.3 Million Records but Only 4.65 Million Emails?

This confused me at first too.

The dataset reportedly contained
```7.3 million total records```

but Have I Been Pwned identified approximately:
```4.65 million unique email addresses```

That doesn't mean another ~2.7 million emails were discovered somewhere else.

The 7.3 million number represents records, not unique email addresses.

The dataset reportedly contained duplicate accounts, and not every record contained a full email address.

So:
```7.3M TOTAL RECORDS -> some duplicate accounts + some records without full emails -> ~4.65M UNIQUE EMAILS```

That distinction is really important when talking about the size of the incident.

### So Was Chess.com Actually Hacked?
Based on the information currently available, there is no evidence showin that someone broke into Chess.com's internal database and downloaded millions of users. 

That's why it seems like automated data harvesting.

### What We Still Don't Know
This is probably the most important part.

We do not know the complete technical attack chain. 

We currently don't know the exact 2026 endpoint used for the email lookup. 

We don't know exactly what that lookup returned. 

We don't know whether one request or multiple requests were used to collect the information. 

And we don't know the exact source of every field contained in the dataset. 

So rather than guessing, I'm going to mark those parts of the attack chain as unknown. That leaves us with:
```External email list -> Chess.com lookup -> Matching account ->  Additional account information -> Automated collection -> 7.3M records```

### More Information and Insight...
1. What is a UUID? A UUID, or Universally Unique Identifier, is simply a unique identifier that a system can use to identify something, like a specific user or account. It can look something like `550e8400-e29b-41d4-a716-446655440000`

But why is this important? In this case, the Chess.com dataset contained version 1 UUID's, and each one of these UUID's contained a timestamp from when it was created.

This gave the researchers a way to check whether the leaked Chess.com account data was legitimate.

Thinking about it like this, let's say I created a Chess.com account right now, the current date and time of writing this post: September 16, 2026 at 7:58 PM, at the same time a decoded UUID timestamp is created for the same time. Researchers could compare the timestamp hidden inside the UUID with the account's actual registration date.

And so when those timestamps matched, it provided them with the evidence that the UUIDs in the dataset were legitimate Chess.com identifiers. 

2. But now, the most interesting part of this whole blog the `gam_audiences` and `audiences_member_of` fields.

These fields were reported to contain audience segmentation information, such as trial eligibility, experiment groups, lapsed-user groups, and rating-based targeting. 

This stands out because this type of information is very different from normal public profile information like a username, country, or chess rating. 

So this raises an interesting question: if this data was collected through scraping, how was this additional information obtained? 

But at this point, we simply don't know. Without knowing the exact endpoint or requests used by the scraper, we shouldn't assume where these fields came from.


### Sources
https://haveibeenpwned.com/Breach/Chess2026 
https://securityaffairs.com/197174/breaking-news/chess-com-leak-exposes-7-3-million-users-evidence-points-to-scraping.html
