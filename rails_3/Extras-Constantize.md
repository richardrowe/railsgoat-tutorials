# Description

The constantize method is a Rails MetaProgramming method designed to dynamically find a constant that matches the string specified. This is often used to dynamically instantiate a class or module. When user-supplied input is a part of that equation, great precautions must be taken to ensure security holes are not introduced.

# Bug

Within the file app/controllers/benefit_forms_controller.rb:

```ruby
def download
  begin
    path = Rails.root.join('public', 'docs', params[:name])
    file = params[:type].constantize.new(path)
    send_file file, :disposition => 'attachment'
  rescue
    redirect_to user_benefit_forms_path(:user_id => current_user.user_id)
  end
end
```

The location of the file to render is dynamically generated based on user input (params[:name]). This means the user controls the location of the file to be retrieved. Additionally, the params[:type] (File) is not validated to make sure it matches up with expected values.

# Hint

It can be very helpful for employees to download benefit forms.