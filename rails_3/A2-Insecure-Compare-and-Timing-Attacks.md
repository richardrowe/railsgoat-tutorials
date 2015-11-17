# Description

A timing attack can exist in several forms. This specific case relates to username (email address) enumeration. By leveraging an automated tool, an attacker can review any subtle variation in response times after submitting a login request to determine if the application is performing a computationally intense function. Meaning, if a function is run once a user is discovered, even if the password is incorrect, this information provides the user with valid or invalid usernames.

# Bug

Within app/models/user.rb

```ruby
def self.authenticate(email, password)
 auth = nil
  user = find_by_email(email)
  raise "#{email} doesn't exist!" if !(user)
   if user.password == Digest::MD5.hexdigest(password)
     auth = user
   else
    raise "Incorrect Password!"
   end
 return auth
end
```
Ignore for a moment that the application actually tells you whether or not an email address exists :-). Instead, let's look at what would happen if this error message wasn't so specific. Even if the error message vulnerability was mitigated (because it indicates whether or not a user exists), there will be some variations in the application's response between a user that exists and one that does not (however so slight, considering MD5 is in use).

To understand why, let's follow the flow of this code example. Firstly, the application looks for a user by email. If not found, nothing else really happens. No further processing, password comparison, etc. If a user _is_ found, we will perform a password comparison and process as normal.

# Hint

Timing is everything. Authenticating is important too.