Heads up there isn't a lot to explain because a lot of the insights about the data breach aren't publicly disclosed yet so bare that in mind as you read through it.

## What happened?

Back in August 2026, a dataset containing more than 7.3 million Chess.com records appeared on a forum called BreachForums. At first, this sounds like a normal data breach where someone hacking into Chess.com and was able to dump a bunch of information from a database.

However... the available evidence points more toward automated data harvesting, also commonly reffered to as scraping.

The important difference is that there is currently no evidence showing that the person who created the dataset broke into chess.com's internal database. Instead, it appears that information was collected through Chess.com's web-facing systems over time. 

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
Chess.com says its lookup functionality was used to find profile information associated with matching email addresses. Which was a functionality that was abused during their 2023 breach as well...

The Basic idea is:
``` Existing email address -> Chess.com lookup -> Does it match a Chess.com account -> YES / NO ```

It's that simple, If there was no match, that email wasn't useful for finding a Chess.com account. 

If there was a match, the email could now be connected to a Chess.com user.

For example:
``` john@example.com -> Chess.com lookup -> ChessPlayer123 (username) ```
That connection is important

Before, someone had an email address.

Great, Now they potentially know:
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

To be quite honest, a lot of this information is already public information that can nroamlly be associated with a Chess.com profilke, such as Usernames, ratings, titles, user-provided profile information (location, phone number, email), and friends.

However, the interesting thing is that we do not know exactly how every field in the leaked dataset was obtained.

For example, the public reporting does not tell us exactly which 2026 request returned:
```
UUID
user ID
verification status
activation status
locale
```

** I will very briefly talk about `gam_audiences` and `audiences_member_of` at the end because those are the most interesting... **



