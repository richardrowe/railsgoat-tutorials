# Description

Applications frequently redirect users to other pages, or use internal forwards in a similar manner. Sometimes the target page is specified in an unvalidated parameter, allowing attackers to choose the destination page. Detecting unchecked redirects is easy. Look for redirects where you can set the full URL. Unchecked forwards are harder, because they target internal pages.

Railsgoat allows the redirection to the paths previously requested but for which the user did not have access. Following authentication, the user is redirected.

# Bug

The application performs zero validation of the path for which they will redirect users, following authentication. The URL parameter is used to determine where to redirect the user, if the url parameter is not present, the user will be redirect to their home page.

app/controllers/sessions_controller.rb

```ruby
def create
  path = params[:url].present? ? params[:url] : home_dashboard_index_path
  begin
    # Normalize the email address, why not
    user = User.authenticate(params[:email].to_s.downcase, params[:password])
   # @url = params[:url]
  rescue Exception => e
  end
  
  if user
    session[:user_id] = user.user_id if User.where(:user_id => user.user_id).exists?
    redirect_to path
  else
    # Removed this code, just doesn't seem specific enough!
    #  flash[:error] = "Either your username and password is incorrect"
    flash[:error] = e.message
    render "new"
  end
end
```

# Hint

Read the description section, fairly big hint there.