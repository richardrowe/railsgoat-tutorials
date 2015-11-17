# Description

Applications frequently use the actual name or key of an object when generating web pages. Applications don’t always verify the user is authorized for the target object. This results in an insecure direct object reference flaw. Testers can easily manipulate parameter values to detect such flaws. Code analysis quickly shows whether authorization is properly verified.

# Bug

Within the app/controllers/work_info_controller.rb file the follow code can be found:

```ruby
def index
  @user = User.find_by_user_id(params[:user_id])
  if !(@user) || @user.admin
    flash[:error] = "Sorry, no user with that user id exists"
    redirect_to home_dashboard_index_path
  end
end
```
Instead of using the current_user object which, takes the user ID value from the user's session and is normally resilient against tampering, the user ID is pulled from the request parameter (user id in the RESTful URL). Additionally, even in the session, User IDs should be sufficiently random and the sessions stored in a persistent manner (ActiveRcord) versus using the Base64 encoded / HMAC validation session schema.

# Hint

Hmmm, that's a lot of info under work info, hope that is secure

