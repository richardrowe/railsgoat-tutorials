# Description

Regular expressions are a common way to extract the data you want from the data you do not want. It is common for Ruby developers to forget that in Ruby regexp anchors are \A and \z. This allows strict enforcement so that potentially dangerous characters such as the newline character aren't able to bypass security-based regular expression checks.

# Bug

Within the file app/controllers/api/v1/users_controller.rb:

```ruby
before_filter :valid_api_token
before_filter :extrapolate_user
```

The above two lines specify that we will run these validations prior to allowing a user to interact with the API endpoints.

```ruby
def valid_api_token
  authenticate_or_request_with_http_token do |token, options|
    # TODO :add some functionality to check if the HTTP Header is valid
    identify_user(token)
  end
end

def identify_user(token="")
   # We've had issues with URL encoding, etc. causing issues so just to be safe
   # we will go ahead and unescape the user's token
   unescape_token(token)
   @clean_token =~ /(.*?)-(.*)/
   id = $1
   hash = $2
   (id && hash) ? true : false
   check_hash(id, hash) ? true : false
end

def check_hash(id, hash)
 digest = OpenSSL::Digest::SHA1.hexdigest("#{ACCESS_TOKEN_SALT}:#{id}")
 hash == digest
end

# We had some issues with the token and url encoding...
# this is an attempt to normalize the data.
def unescape_token(token="")
  @clean_token = CGI::unescape(token)
end
```

This first validation, valid_api_token, extracts the user's access token. Within the token there is a user ID and a hash. The application extracts both values, hashes the user ID and the application's secret salt together. If the digest hash matches with the user provided hash, the entire token is valid. 

Meaning, if the hash (check_hash) doesn't match the hash provided by the user, the token is invalid and therefore unauthorized. Alternatively, the hash provided is valid but the user ID is invalid. 

The next validation, built after this check, extrapolates the user from that hash. In theory, because we have already validated both the user ID and hash are valid, we can just extract the user ID from what has been provided and determine user access.

```ruby
# Added a method to make it easy to figure out who the user is.
def extrapolate_user
  @user = User.find_by_id(@clean_token.split("-").first)
end
```

Unfortunately, we've made a mistake. The regular expression can be bypassed by entering a newline character (url encoded: %0a).We meant or expected for a user to enter a token such as:

    Authorization: Token token=1-01de24d75cffaa66db205278d1cf900bf087a737

However, the user actually enters:

    Authorization: Token token=2%0a1-01de24d75cffaa66db205278d1cf900bf087a737

This means that our token will pass the initial hash check. Additionally, when we perform the split by the hyphen ("-") character, and retrieve the first value from the newly created array (what should be a valid user ID), it will be "2\n1". When performing a `find_by_*` on an integer column (`to_i` is used), ActiveRecord will ignore everything from the newline character on and return the result of the first character. This means, we can become another user!

# Hint

An API token? Interested to see what calls I can make! What are the closing tags for Ruby again?