# Unit 2
## Chapter 6: Adding A User Gravatar As A New User

In this chapter, you will allow new users to add their gravatar email addresses.

### New Branch
Enter the command "git checkout -b 02-06-user_gravatar_new".

### Integration Test
* Enter the command "rails generate integration_test gravatar_new".
* Edit the file test/integration/gravatar_new_test.rb.  Replace everything between the line "class GravatarNewTest < ActionDispatch::IntegrationTest" and the final "end" line with the following:
```
  test 'new user can sign up with a gravatar email address' do
    visit root_path
    click_on 'Sign up now!'
    fill_in('Last name', with: 'Norris')
    fill_in('First name', with: 'Chuck')
    fill_in('Username', with: 'cnorris')
    fill_in('Email', with: 'gmail@chucknorris.com')
    fill_in('Gravatar email', with: 'rails_in_windows@chucknorris.com')
    fill_in('Password', with: 'I push the Earth down!')
    fill_in('Password confirmation', with: 'I push the Earth down!')
    click_button('Sign up')

    open_email('gmail@chucknorris.com')
    current_email.click_link 'Confirm my account'
    login_user('gmail@chucknorris.com', 'I push the Earth down!', false)
    click_on 'Edit Settings'
    page.assert_selector(:xpath, xpath_input_str('rails_in_windows@chucknorris.com'))
end
```
* Enter the command "sh test_app.sh".  Both of your new integration tests fail.
* Enter the command "alias test1='(command to run failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  Both new integration tests fail.

### Wrapping Up
* Enter the command "sh git_check.sh". All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Allow new users to provide a gravatar email address"
git push origin 02-06-user_gravatar_new
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
