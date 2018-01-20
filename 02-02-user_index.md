# Unit 2
## Chapter 2: User Index

In this chapter, you will allow users to view the list of all users.

### New Branch
Enter the command "git checkout -b 02-02-user_index".

### Controller Test
* Edit the file test/controllers/users_controller_test.rb. Replace the "index user" section with the following:
```
  # BEGIN: index user
  test 'should not redirect index page when logged in as a user' do
    sign_in @u1, scope: :user
    get users_path
    assert_response :success
  end
  # END: index user
```
* Enter the command "sh testc.sh".  Your new controller test fails.
* Edit the file app/controllers/users_controller.rb.  Replace the definition of "may_index_user" with the following:
```
  def may_index_user
    return redirect_to(root_path) unless admin_signed_in? || user_signed_in?
  end
```
* Enter the command "sh testc.sh".  Now all of the controller tests should pass.

### Integration Test
* Enter the command "sh test_app.sh". The controller tests pass, but an integration test of the user index fails.
* Enter the command "alias test1='(Command for running failed tests minus the TESTOPTS portion)'"
* Enter the command "test1". An integration test needs to be updated.
* Edit the file test/integration/user_index_test.rb.
  * Replace the "users index page is not accessible to users" test with the following:
  ```
    test 'users index page is accessible to users' do
      check_index_enabled_for_user(@u1)
      check_index_enabled_for_user(@u2)
      check_index_enabled_for_user(@u3)
      check_index_enabled_for_user(@u4)
      check_index_enabled_for_user(@u5)
      check_index_enabled_for_user(@u6)
      check_index_enabled_for_user(@u7)
    end
  ```
  * Replace the definition of check_index_disabled_for_user with the following:
  ```
    def check_index_enabled_for_user(u)
      login_as(u, scope: :user)
      check_index_enabled
    end
  ```
* Enter the command "test1".  Now a test fails because the user does not see a "User Index" link.
* Edit the file app/views/layouts/_header.html.erb.  At the beginning of the user section, add the following line:
```
          <li><%= link_to "User Index",   users_path %></li>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Allowed users to view the user index"
git push origin 02-02-user_index
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
