# Description

ActiveRecord provides a useful tool for it's Models called a scope. In the words of the documentation:

>"Scoping allows you to specify commonly-used queries which can be referenced as  method calls on the association objects or models."

This means that we can call a scope as a method and that the scope can be used for common queries such as where and join. Developers must be careful not to interpolate or concatenate user input into these scope calls as this can lead to SQL Injection. This is a common mistake made and can have serious consequences.

# Bug

Within app/models/analytics.rb:

```ruby
class Analytics < ActiveRecord::Base
  attr_accessible :ip_address, :referrer, :user_agent

  scope :hits_by_ip, ->(ip,col="*") { select("#{col}").where(:ip_address => ip).order("id DESC")}

  def self.count_by_col(col)
    calculate(:count, col)
  end
```

Additionally, within app/controllers/admin_controller.rb:

```ruby
def analytics
  if params[:field].nil?
    fields = "*"
  else
    fields = params[:field].map {|k,v| k }.join(",")
  end

  if params[:ip]
    @analytics = Analytics.hits_by_ip(params[:ip], fields)
  else
    @analytics = Analytics.all
  end
  render "layouts/admin/_analytics"
end
```

Within the controller we call the method hits_by_ip. This method is actually a scope as highlighted (above) in the Analytics model. The field object, defined within the controller, represents user-input that is intended to control the column returned by the SQL query. The field object represents the HTTP Request's parameter key. So this means we can control at least a portion of the query. Due to the fact that this input is used as an interpolated value within the query string, we have control over a larger portion of the query.

# Hint
Administrative analytics functionality need further security analysis. Now might be a good time to test for SQLi.