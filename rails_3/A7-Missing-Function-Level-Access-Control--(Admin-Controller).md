# Description

Many web applications check URL access rights before rendering protected links and buttons. However, applications need to perform similar access control checks each time these pages are accessed, or attackers will be able to forge URLs to access these hidden pages anyway.

# Bug

Rails provides the ability to apply before_filter(s) which run prior to rendering content to the user. This is helpful when restricting access to content based on the user's role. These filters can be skipped on certain actions or controllers and entirely if certain conditions are met. In this case, the before_filter is being skipped if the admin_id param is equal to 1.

```ruby
class AdminController < ApplicationController

  before_filter :administrative, :if => :admin_param
  
  ...
  
  def admin_param
    params[:id] != '1'
  end
```

# Hint

I bet there is some admin functionality in here :-)
