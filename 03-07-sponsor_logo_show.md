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
* Add the line "public/uploads/" to the end of the .gitignore file.  This is necessary to keep the uploaded and seeded pictures out of the source code.
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
* Enter the command "sh git_check.sh".
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

### Updating the Sponsor Model

### Seeding the Database

### Wrapping Up
* Enter the command "git push origin 03-07-sponsor_logo_show".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch.  Once the branch has been merged, you may click on the option to delete it.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
