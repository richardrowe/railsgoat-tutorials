# Description

Another one of the Rails security configurations relates to escaping HTML entities in JSON.

# Bug

When the following setting is set to false, HTML entities in JSON response will not be encoded.

```ruby
ActiveSupport::escape_html_entities_in_json = false
```

# Hint

Think HTML entities, escaping and initializers.



