# Unit 2
## Chapter 1: User Profiles

In this chapter, you will allow users to view the profiles of other users.

### New Branch
Enter the command "git checkout -b 02-01-user_show".

### Controller Test
* Edit the file test/controllers/users_controller_test.rb.  Replace everything within the show-other_user section with the following:
```
  test 'should not redirect users from the profiles of other users' do
    sign_in @u1, scope: :user
    get user_path(@u2)
    assert_response :success
    get user_path(@u3)
    assert_response :success
    get user_path(@u4)
    assert_response :success
    get user_path(@u5)
    assert_response :success
    get user_path(@u6)
    assert_response :success
    get user_path(@u7)
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  Your new controller test fails.
* Edit the file app/controllers/users_controller.rb.
  * Delete the definitions of correct_user and admin_or_correct_user.
  * Remove the line designating admin_or_correct_user as a helper method.
  * Replace the definition of may_show_user with the following:
  ```
    def may_show_user
      return redirect_to(root_path) unless admin_signed_in? || user_signed_in?
    end
  ```
* Enter the command "sh testc.sh".  All tests should now pass.

### Integration Test
* Enter the command "sh test_app.sh".  The controller tests pass, but an integration test of the user profile fails.
* Enter the command "alias test1='(Command for running failed tests minus the TESTOPTS portion)'"
* Enter the command "test1".  An integration test needs to be updated.
* In the file test/integration/user_show_test.rb, rename the "user may not view profiles of other users" test as the "user can view profiles of other users" test.
* Replace the definition of user_view_other_profile with the following:
```
  def user_view_other_profile(u1, u2)
    login_as(u1, scope: :user)
    check_profile_enabled(u2)
  end
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Allowed users to view other user profiles"
git push origin 02-01-user_show
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
