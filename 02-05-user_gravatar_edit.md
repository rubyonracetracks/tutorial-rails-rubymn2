# Unit 2
## Chapter 5: User Gravatar Edit

In this chapter, you will users to edit their gravatar email addresses or their gravatars themselves.

### New Branch
Enter the command "git checkout -b 02-05-user_gravatar_edit".

### Integration Test
* Enter the command "rails generate integration_test gravatar_edit".
* Edit the file test/integration/gravatar_edit_test.rb.  Replace everything between the line "class GravatarEditTest < ActionDispatch::IntegrationTest" and the final "end" line with the following:
```
  test 'user edit page provides a link to editing the gravatar' do
    login_as(@u1, scope: :user)
    visit root_path
    click_on 'Edit Settings'
    assert page.has_link?('Edit Gravatar', href: 'http://gravatar.com/emails')
  end

  test 'user can edit gravatar_email' do
    g_email = 'henry_jones@example.com'
    login_as(@u1, scope: :user)
    visit root_path
    click_on 'Edit Settings'
    fill_in('Gravatar email', with: g_email)
    fill_in('Current password', with: 'Goldfinger')
    click_button('Update')
    assert page.has_text?('Your account has been updated successfully.')
    click_on 'Edit Settings'
    page.assert_selector(:xpath, xpath_input_str(g_email))
  end
```
* Enter the command "sh test_app.sh".  Both of your new integration tests fail.
* Enter the command "alias test1='(command to run failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  Both new integration tests fail.

### User Edit Page
* In the file app/views/users/registrations/edit.html.erb, add the following code immediately after the if statement that begins with "if devise_mapping.confirmable?":
```

  <div class="field">
    <%= f.label :gravatar_email %> (OPTIONAL)<br />
    <%= f.email_field :gravatar_email %>
  </div>

```
* In the file app/views/users/registrations/edit.html.erb, add the following code immediately before the line containing the command "end" (signifying the end of the form):
```
  <%= gravatar_for @user %>
  <a href="http://gravatar.com/emails" target="_blank">Edit Gravatar</a>
```
* Enter the command "test1".  One integration test still fails, because submitting a new gravatar_email address does not actually change this parameter.

### User Registration Controller
* Edit the file app/controllers/users/registrations_controller.rb.  Add ":gravatar_email" to the list of keys to be permitted within the definition of the configure_account_update_params methods.
* Enter the command "test1".  Now all tests should pass.

### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added user gravatar edit capabilities"
git push origin 02-05-user_gravatar_edit
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
