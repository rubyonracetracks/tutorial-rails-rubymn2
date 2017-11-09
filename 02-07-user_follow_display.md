# Unit 2
## Chapter 7: Displaying User Relationships

In this chapter, you will update the user profile page to show which users are being followed.  This information is to be available only to that particular user and the admins, NOT to other users or the general public.

The profile page lists the number of other users being followed and a link to another page listing all of these other users.

### New Branch
Enter the command "git checkout -b 02-07-follower_display".

### User Controller Test
* Edit the file test/controllers/users_controller_test.rb and add the following code just before the final "end" statement:
```
  # BEGIN: following
  test 'should redirect following when not logged in' do
    visit following_user_path(@u1)
    assert_redirected_to root_path
  end

  test 'should redirect following when logged in as a different user' do
    sign_in @u2, scope: :user
    visit following_user_path(@u1)
    assert_redirected_to root_path
  end

  test 'should not redirect following when logged in as that user' do
    sign_in @u1, scope: :user
    visit following_user_path(@u1)
    assert_response :success
  end

  test 'should not redirect following when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    visit following_user_path(@u1)
    assert_response :success
  end

  test 'should not redirect following when logged in as a super admin' do
    sign_in @a1, scope: :admin
    visit following_user_path(@u1)
    assert_response :success
  end
  # END: following
```
* Enter the command "sh testc.sh".  5 tests fail because following_user_path is undefined.
* Add the following code just before the end of the user section in config/routes.rb:
```
  resources :users do
    member do
      get :following
    end
  end
```
* Enter the command "sh testc.sh".  Now the 5 tests fail because the following action is not defined in the user controller.
* In the file app/controllers/users_controller.rb, add the following code just before the end of the action section:
```
  def following
    @title = 'Following'
    @user  = User.find(params[:id])
    @users = @user.following.paginate(page: params[:page])
    render 'show_follow'
  end
```
* In the file app/controllers/users_controller.rb, add the following code just before the end of the before_action section:
```
before_action :may_show_following, only: [:following]
```
* In the file app/controllers/users_controller.rb, add the following code just before the end of the private section:
```
  def correct_user
    current_user == User.find(params[:id])
  end

  def admin_or_correct_user
    correct_user || admin_signed_in?
  end
  helper_method :admin_or_correct_user

  def may_show_following
    return redirect_to(root_path) unless admin_or_correct_user
  end
  helper_method :may_show_following
```
* Enter the command "sh test.sh".  Now 3 tests fail due to a missing template.
* Enter the command "touch app/views/users/show_follow.html.erb".
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".

### Integration Test
* Enter the command "rails generate integration_test user_following".
* Edit the resulting test/integration/user_following_test.rb.  Replace the text between the line "class UserFollowingTest < ActionDispatch::IntegrationTest" and the last "end" line with the following:
```

```



### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the follower display"
git push origin 02-07-follower_display
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
