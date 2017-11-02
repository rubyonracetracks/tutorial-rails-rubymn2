# Unit 2
## Chapter 5: User Gravatar Edit

In this chapter, you will allow new users to sign up with a gravatar email address and allow existing users to edit their gravatar email addresses or their gravatars themselves.

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
    page.assert_selector(:xpath, xpath_input_str(g_email))
  end

  test 'user can add gravatar_email at signup' do
    visit root_path
    click_on 'Sign up now!'
    fill_in('Last name', with: 'Magnum')
    fill_in('First name', with: 'Thomas')
    fill_in('Username', with: 'magnum_pi')
    fill_in('Email', with: 'tmagnum@example.com')
    fill_in('Gravatar email', with: 'tom_selleck@example.com')
    fill_in('Password', with: 'Work the lock!')
    fill_in('Password confirmation', with: 'Work the lock!')
    click_button('Sign up')
    open_email('tmagnum@example.com')
    current_email.click_link 'Confirm my account'
    click_on 'Edit Settings'
    page.assert_selector(:xpath, xpath_input_str('tom_selleck@example.com'))
  end
```
* Enter the command "sh test_app.sh".  All 3 of your new integration tests fail.
* Enter the command "alias test1='(command to run failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The 3 new integration tests fail.

### User Edit Page
* In the file app/views/users/registrations/edit.html.erb, add the following code immediately after the field for entering the email address:
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
* Enter the command "test1".  Now only 2 of the new integration tests fail.

### User Registration Controller
* Edit the file app/controllers/users/registrations_controller.rb.  Add ":gravatar_email" to the list of keys to be permitted within the definitions of the configure_sign_up_params and configure_account_update_params methods.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added user gravatar edit capabilities"
git push origin 02-05-user_gravatar_edit
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
