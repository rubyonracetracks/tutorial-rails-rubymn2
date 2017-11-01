# Unit 2
## Chapter 4: User Gravatar Displays

In this chapter, you will add user gravatar displays to the user profile pages and user index page.

### New Branch
Enter the command "git checkout -b 02-04-user_gravatar_display".

### User Profile
* Create the file app/helpers/users_helper.rb and give it the following contents:
```
# rubocop:disable Style/ColonMethodCall
# rubocop:disable Metrics/LineLength

#
module UsersHelper
  # Returns the Gravatar for the given user.
  def gravatar_for(user)
    gravatar_id = Digest::MD5::hexdigest('default@rubyonracetracks.com')
    gravatar_id = Digest::MD5::hexdigest(user.gravatar_email.downcase) if user.gravatar_email?
    gravatar_url = "https://secure.gravatar.com/avatar/#{gravatar_id}"
    image_tag(gravatar_url,
              alt: "#{user.first_name} #{user.last_name}",
              class: 'gravatar')
  end
end

# rubocop:enable Style/ColonMethodCall
# rubocop:enable Metrics/LineLength
```
* In the app/views/users/show.html.erb file, add the following code just before the h1 header display:
```
    <%= gravatar_for @user %>
```
* In your web browser, log into your local version of your app, click on "User Index", and view the profiles of the seeded users.  The gravatar should be present on the user profile pages.
* Enter the command "sh git_check.sh".  All tests should pass, but Brakeman flags your gravatar_for method as a security issue due to the use of the weak MD5 hashing algorithm.
* Enter the command "bundle exec brakeman -Aq -w2 --no-pager -I".  Because there is no input file yet, you must continue with an empty config.  When prompted, select the option to inspect all warnings.  When prompted on what to do with your weak MD5 hashing algorithm, select the option to add this warning to the ignore list.
* Note that the file config/brakeman.ignore has been created and now contains the issue to be ignored.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### User Index
* In the file app/views/users/index.html.erb, replace everything between the two "will_paginate" statements with the following:
```
<%= render @users %>
<hr>
```
* Replace the contents of app/views/users/_user.html.erb with the following:
```

```

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the user gravatar displays"
git push origin 02-03-user_gravatar_display
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
