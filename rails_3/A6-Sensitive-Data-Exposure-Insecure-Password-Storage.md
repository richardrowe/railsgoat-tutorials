# Description

The OWASP description - Many web applications do not properly protect sensitive data, such as credit cards, SSNs, and authentication credentials, with appropriate encryption or hashing. Attackers may steal or modify such weakly protected data to conduct identity theft, credit card fraud, or other crimes.

Railsgoat does hash user passwords. Unfortunately, it does so using an extremely weak algorithm (MD5). Generally speaking, a strong algorithm and per-user salt can greatly improve the security of a hashed value. Also important to note, hashing and encryption are not the same. Encryption is meant to be reversible using some secret information, hashing is not, hashing is a one-way function not meant to be reversible.

All that being said, there are groups within security organizations that devote themselves to threat models built around this topic so clearly, this description does not encompass all scenarios. However, our recommendation is better than hashing using MD5.

# Bug

Within app/models/user.rb:

```ruby
before_save :hash_password

def self.authenticate(email, password)
 auth = nil
 user = find_by_email(email)
 if user
   if user.password == Digest::MD5.hexdigest(password)
     auth = user
   else
    raise "Incorrect Password!"
   end
 else
    raise "#{email} doesn't exist!"
 end
 return auth
end

def hash_password
  if self.password.present?
      self.password = Digest::MD5.hexdigest(password)
  end
end
```

# Hint

How protected are those passwords in the database against cracking?