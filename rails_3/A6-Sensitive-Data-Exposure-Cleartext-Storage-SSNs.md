# Description

The Railsgoat application stores and transmits Social Security Numbers insecurely.

# Bug

The Railsgoat application stores user's Social Security Numbers in plain-text within the database and because of this, it fails to adequately protect these numbers from theft. Additionally, the user's full SSN is sent back to the user within an HTTP response from the application.

The WorkInfo model (app/models/work_info.rb) is missing code to encrypt this data prior to storage. Additionally, while code exists to render only the last 4 numbers of an SSN (shown below), at no time is it used.

```ruby
# We should probably use this
def last_four
  "***-**-" << self.decrypt_ssn[-4,4]
end
```

# Hint

My SSN seems pretty important, hope it's kept safe!