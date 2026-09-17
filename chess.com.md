Heads up: there isn't a lot to explain because a lot of the technical details surrounding the incident aren't publicly disclosed, so bear that in mind.

## What happened?

In August 2026, a dataset containing more than 7.3 million Chess.com records appeared on a forum called BreachForums. At first, this sounds like a normal data breach where someone hacked into Chess.com and was able to dump a bunch of information from a database.

However, the available evidence suggests something more like automated data harvesting, also commonly referred to as scraping.

The important difference is that there is currently no evidence showing that the person who created the dataset broke into Chess.com's internal database. Instead, it appears that information was collected through automated requests. 

### Step 1: Start with Existing Email Addresses

The individual who created the dataset appears to have started with a large list of existing email addresses.

This idea is supported by Have I Been Pwned. Of the approximately 4.65 million unique email addresses found in the dataset, HIBP reported that 99% had already appeared in previous data breaches.

So imagine starting with:
```
john@example.com
sarah@example.com
mike@example.com
```
At this point, these are just email addresses. We don't necessarily know whether any of them have Chess.com accounts.

### Step 2: Check the Emails Against Chess.com

The next step appears to have involved checking the email addresses against Chess.com to determine whether they were associated with Chess.com accounts. 

If an email matched an account, the person collecting the data could then associate that email with additional Chess.com account information.

The basic idea is:
``` Existing email address -> Chess.com lookup -> Does it match a Chess.com account -> YES / NO ```

However, we don't know exactly how this check was performed. The exact endpoint, request, or method used to connect these email addresses to Chess.com accounts has not been publicly disclosed.

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

To be quite honest, a lot of this information can already be associated with a public Chess.com profile, such as usernames, ratings, titles, names, country, location, and account creation dates. 

However, the interesting thing is that we do not know exactly how every field in the leaked dataset was obtained.

For example, the public reporting does not tell us exactly which 2026 request returned:
```
UUID
verification status
activation status
locale
```

**I will very briefly talk about `gam_audiences` and `audiences_member_of` at the end because those are the most interesting...**

### Step 4: Automate the Process

Doing this manually for millions of email addresses would take forever. 

Instead, the evidence suggests the collection process was automated.

```Take an email -> Check Chess.com -> Account found? -> Collect available information -> Save it -> Move to next email -> Repeat```

Researchers found that the records appeared to have been collected over nine consecutive days, rather than all at once. They also found duplicate user IDs, meaning some Chess.com accounts appeared in the dataset more than once. 

This pattern supports the idea that the dataset was created through automated harvesting over time, rather than someone simply downloading a Chess.com database all at once.


### Why Are There 7.3 Million Records but Only 4.65 Million Emails?

This confused me at first, but 7.3 million records does not mean 7.3 million unique email addresses. The dataset contained around 7.3 million total rows, while only about 4.65 million unique email addresses were identified. 

There are a couple reasons for this. Some records did not contain a full email address, and some Chess.com accounts appeared in the dataset more than once. Researchers reported that roughly 7.4% of the records were duplicates.

So simply:
```7.3 million total records ≠ 7.3 million unique emails```

### So Was Chess.com Actually Hacked?

Based on the evidence available, there is currently no evidence that someone directly hacked into Chess.com's internal database or servers. 

Instead, the evidence points toward automated scraping and data harvesting, where information was collected and combined over time. 

That doesn't mean the incident isn't important. Millions of email addresses were connected with Chess.com account information, making the dataset much more useful for things like phishing and social engineering.

### What We Still Don't Know

There are still some important gaps in the attack chain. We don't know the exact endpoint or requests used to match the emails to Chess.com accounts, what information those requests returned, or exactly where every field in the dataset came from. 

We also don't know whether the scraper used one request or multiple requests to gather all of the additional account information. 

Because these technical details haven't been publicly disclosed, we can't fully recreate exactly how the scraping process worked.

### More Information and Insight...
1. What is a UUID?
A UUID, or Universally Unique Identifier, is simply a unique identifier that a system can use to identify something, like a specific user or account. It can look something like:
`6ba7b810-9dad-11d1-80b4-00c04fd430c8`

But why is this important? In this case, the Chess.com dataset contained version 1 UUIDs, and each one of these UUIDs contained a timestamp from when it was created.

This gave the researchers a way to check whether the leaked Chess.com account data was legitimate.

Think about it like this: let's say I created a Chess.com account right now, on September 16, 2026, at 7:58 PM. At around the same time, a version 1 UUID is associated with my account. Since that UUID contains a timestamp, researchers could decode it and compare it with the time my account was created. If the two timestamps match, it provides strong evidence that the UUID and account information are legitimate.

Researchers did this across a sample of 200,000 records and found that the UUID timestamps matched the Chess.com account creation dates. This gave them strong evidence that the data was legitimate.

2. But now, the most interesting part of this whole blog is the `gam_audiences` and `audiences_member_of` fields.

The `gam_audiences` field appears to refer to Google Ad Manager audiences, or groups used for advertising and targeting, while `audiences_member_of` indicates which of those audience groups a particular user belonged to.

These fields were reported to contain audience segmentation information, such as trial eligibility, experiment groups, lapsed-user groups, and rating-based targeting. 

This stands out because this type of information is very different from normal public profile information like a username, country, or chess rating. 

So this raises an interesting question: if this data was collected through scraping, how was this additional information obtained? 

At this point, we simply don't know. Without knowing the exact endpoint or requests used by the scraper, we shouldn't assume where these fields came from.


### Sources
https://haveibeenpwned.com/Breach/Chess2026 
https://securityaffairs.com/197174/breaking-news/chess-com-leak-exposes-7-3-million-users-evidence-points-to-scraping.html
https://optmsg.com/blog/breach-breakdown/chess-com-data-breach/
https://news.ycombinator.com/item?id=49691584
If you want a better understanding of the structure of UUIDs, you can check out: https://www.sohamkamani.com/uuid-versions-explained/
