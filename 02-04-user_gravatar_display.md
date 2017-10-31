# Unit 2
## Chapter 4: User Gravatar Displays

In this chapter, you will add user gravatar displays to the user profile pages and user index page.

### New Branch
Enter the command "git checkout -b 02-04-user_gravatar_display".

### User Profile
* Create the file app/helpers/users_helper.rb and give it the following contents:
```
# rubocop:disable Style/ColonMethodCall

#
module UsersHelper
  # Returns the Gravatar for the given user.
  def gravatar_for(user)
    gravatar_id = Digest::SHA256::hexdigest(user.email.downcase)
    gravatar_url = "https://secure.gravatar.com/avatar/#{gravatar_id}"
    image_tag(gravatar_url,
              alt: "#{user.first_name} #{user.last_name}",
              class: 'gravatar')
  end
end

# rubocop:enable Style/ColonMethodCall
```
* In the app/views/users/show.html.erb file, add the following code just before the h1 header display:
```
    <%= gravatar_for @user %>
```
* In your web browser, log into your local version of your app, click on "User Index", and view the profiles of the seeded users.  The gravatar should be present on the user profile pages.

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
