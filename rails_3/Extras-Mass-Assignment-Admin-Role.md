# Description

The application allows the admin attribute of a User model to be set through a mass assignment call. This vulnerability exists because a developer has indicated it is acceptable to set or change the admin value through the use of the attr_accessible setting. Any action that uses mass assignment to create a user or modify a user's settings is susceptible to this attack which would allow vertical privilege escalation.

# Bug

The bug is introduced within app/models/user.rb, seen on line 3 (:admin):

```ruby
class User < ActiveRecord::Base
  attr_accessible :email, :password, :admin, :password_confirmation, :first_name, :last_name
```

Any attribute added to the attr_accessible setting can be used during a mass assignment call. What this means is that conceptually, the following is allowed:

```ruby
# Note the string "true"/"false" or 1/0, etc. can be added to specify the boolean attribute...
# is true or false thanks to ActiveRecord
User.new(
  :email => "email@email.com",
  :admin => "true",
  :password => "h4xx0r",
  :first_name => "Captain",
  :last_name => "Crunch"
)
```

# Hint

Did you register your account correctly? How about when you updated your settings?