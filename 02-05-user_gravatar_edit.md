# Unit 2
## Chapter 5: User Gravatar Edit

In this chapter, you will allow users to edit their gravatar email addresses or their gravatars themselves.  (Editing the gravatars requires providing a link to do so on the user edit page.)

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
    assert page.has_link?('Edit Gravatar', href: 'http://gravatar.com/email')
  end

  test 'user can edit gravatar_email' do
    g_email = 'henry_jones@example.com'
    login_as(@u1, scope: :user)
    visit root_path
    click_on 'Edit Settings'
    fill_in('Email', with: g_email)
    click_button('Update')
    assert page.has_text?('Your account has been updated successfully.')
    page.assert_selector(:xpath, xpath_input_str(g_email))
  end
```

### User Edit Page

### User Registration Controller

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
