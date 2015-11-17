# Description

An OS command injection attack occurs when an attacker attempts to execute system level commands through a vulnerable application. Applications are considered vulnerable to the OS command injection attack if they utilize user input in a system level command.

# Bug

This manifestation of the bug occurs within the Benefits model. A system command is used to make a copy of the file the user has chosen to upload. User-supplied input is leveraged in creating this system command.

Within app/controllers/benefits_controller.rb:

```ruby
def upload
  file = params[:benefits][:upload]
  if file
    flash[:success] = "File Successfully Uploaded!"
    Benefits.save(file, params[:benefits][:backup])
  else
    flash[:error] = "Something went wrong"
  end
  redirect_to user_benefit_forms_path(:user_id => current_user.user_id)
end
```
Within app/models/benefits.rb:

```ruby
class Benefits < ActiveRecord::Base
 attr_accessor :backup

 def self.save(file, backup=false)
   data_path = Rails.root.join("public", "data")
   full_file_name = "#{data_path}/#{file.original_filename}"
   f = File.open(full_file_name, "wb+")
   f.write file.read
   f.close
   make_backup(file, data_path, full_file_name) if backup == "true"
 end

 def self.make_backup(file, data_path, full_file_name)
    if File.exists?(full_file_name)
      silence_streams(STDERR) { system("cp #{full_file_name} #{data_path}/bak#{Time.zone.now.to_i}_#{file.original_filename}") }
    end
 end

end
```
The command injection vulnerability is introduced when the user-supplied input (name of file) is interpolated or mixed in with a system command.

# Hint

Let's create a backup when uploading a file, wonder how they are naming it?
