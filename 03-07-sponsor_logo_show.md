# Unit 3
## Chapter 7: Showing the Sponsor Logo

In this chapter, you will add a logo parameter to sponsors.  This particular chapter will focus on creating the model, seeding the database, and displaying the logo.  Giving super admins the ability to add or edit the sponsor logo will be covered in the next chapter.

### New Branch
Enter the command "git checkout -b 03-07-sponsor_logo_show".

### Gemfile
* Add the following lines to the end of the Gemfile:
```
# BEGIN: for sponsor logos
gem 'carrierwave' # For uploading files
gem 'mini_magick' # For resizing images
# END: for sponsor logos
```
* Enter the command "bundle install" to install these new gems.
* Get the currently installed versions of the carrierwave and mini_magick gems by entering the following commands:
```
gem list "^carrierwave$"
gem list "^mini_magick$"
```
* Pin the versions of these two gems in the Gemfile.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the carrierwave and mini_magick gems for sponsor logos"
```

### .gitignore
* Add the following lines "public/uploads/" to the end of the .gitignore file:
```

# Keep uploaded and seeded pictures out of the source code
public/uploads/
```
* Enter the following commands:
```
git add .
git commit -m "Keep uploaded and seeded pictures out of the source code"
```

## Adding the Picture Uploader
* Enter the command "rails generate uploader Picture".
* In the file app/uploaders/picture_uploader.rb, add the line "#" immediately before the line "class PictureUploader < CarrierWave::Uploader::Base".
* In the file app/uploaders/picture_uploader.rb, replace everything between the line "class PictureUploader < CarrierWave::Uploader::Base" and the last "end" statement with the following:
```
  # Include RMagick or MiniMagick support:
  # include CarrierWave::RMagick
  include CarrierWave::MiniMagick

  # Choose what kind of storage to use for this uploader:
  storage :file # Local storage

  # Override the directory where uploaded files will be stored.
  # This is a sensible default for uploaders that are meant to be mounted:
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  process resize_to_limit: [400, 400]

  # Create different versions of your uploaded files:
  version :thumb do
    process resize_to_fit: [50, 50]
  end

  # Add a white list of extensions which are allowed to be uploaded.
  # For images you might use something like this:
  def extension_whitelist
    %w[jpg jpeg gif png]
  end
```
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the picture uploader"
```

### Adding the Picture Parameter to the Sponsor Model
* Enter the following commands:
```
rails generate migration add_picture_to_sponsors picture:string
bin/rails db:migrate RAILS_ENV=test
rails db:migrate

```
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the picture parameter to the sponsor model"
```

### Updating the Sponsor Model
* Edit the file app/models/sponsor.rb.  Add the following lines immediately before the last "end" statement:
```
  # Allows the file uploading process to fill in the picture parameter
  mount_uploader :picture, PictureUploader
```
* Enter the command "sh kill_spring.sh; sh testm.sh".  All tests should pass.  NOTE: If you do NOT kill the Spring server, you may get an error message telling you that Sponsor::PictureUploader is an uninitialized constant.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Allowed the file uploading process to fill in the picture parameter of the sponsor model"
```

### Seeding the Database
* Edit the file db/seeds.rb.  In the create_sponsor function, add the parameter "remote_picture_url" with the value "Faker::Company.logo".
* Go to the tmux window containing the Rails server.  Stop the server, and then enter the command "sh seed.sh; sh server.sh".
* Use pgAdmin to view the sponsors in the database.  For the picture parameter, you should see the *.png file names of each sponsor logo.  These files are stored in the public/uploads/sponsor/picture/ directory.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Successfully seeds sponsor logos"
```

### Displaying the Sponsor Logos
* Edit the app/views/sponsors/show.html.erb file.  Add the following code to the end of the file:
```
<% if @sponsor.picture? %>
  <br><br>
  <%= image_tag @sponsor.picture.url %>
<% end %>
```
* In your browser view of the local site, go to the profile page of any sponsor.  The logo should now appear.
* Edit the app/views/sponsors/index.html.erb file.  For the current sponsors and past sponsors sections, add the following code on the line immediately after "<%= link_to s.name, s %>":
```
      <% if s.picture? %>
        <br>
        <p style="margin-left: 40px">
        <%= image_tag s.picture.url(:thumb) %>
      <% end %>
```
* In your browser view of the local site, go to the sponsor index page.  Thumbnail versions of the logos should now appear.
* Edit the file app/views/static_pages/home.html.erb.  In the "Current Sponsors" section,  add the following code on the line immediately after "<%= link_to s.name, s %>":
```
      <% if s.picture? %>
        <br>
        <p style="margin-left: 40px">
        <%= image_tag s.picture.url(:thumb) %>
      <% end %>
```
* In your browser view of the local site, go to the home page.  Thumbnail versions of the logos should now appear.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Displays sponsor logos"
```

### Wrapping Up
* Enter the command "git push origin 03-07-sponsor_logo_show".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch.  Once the branch has been merged, you may click on the option to delete it.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
