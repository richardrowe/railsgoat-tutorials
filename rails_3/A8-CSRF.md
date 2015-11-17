# Description

A CSRF attack forces a logged-on victim’s browser to send a forged HTTP request, including the victim’s session cookie and any other automatically included authentication information, to a vulnerable web application. This allows the attacker to force the victim’s browser to generate requests the vulnerable application thinks are legitimate requests from the victim.

# Bug

Cross-Site Request Forgery (CSRF) - The following code was taken from: /app/controllers/application_controller.rb and /app/views/layouts/application.html.erb

application_controller.rb

```ruby
# Our security guy keep talking about sea-surfing, cool story bro.
# protect_from_forgery
```

application.html.erb

```erb
<%#= csrf_meta_tags %> <!-- <~ What is this for? I hear it helps w/ JS and Sea-surfing.....whatevz -->
```

# Hint

PTO is precious, glad my calendar is safe!