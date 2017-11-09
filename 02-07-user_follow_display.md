# Unit 2
## Chapter 7: Displaying User Relationships

In this chapter, you will update the user profile page to show which users are being followed.  This information is to be available only to that particular user and the admins, NOT to other users or the general public.

The profile page lists the number of other users being followed and a link to another page listing all of these other users.

### New Branch
Enter the command "git checkout -b 02-06-follower_display".

### User Controller Test
* Edit the file test/controllers/users_controller_test.rb and add the following code just before the final "end" statement:
```
  test 'should redirect following when not logged in' do
    get following_user_path(@user)
    assert_redirected_to login_url
  end
```
* Add the following code just before the end of the user section in config/routes.rb:
```
  resources :users do
    member do
      get :following
    end
  end
```
* In the file app/controllers/users_controller.rb, add the following code just before the end of the action section:
```
  def following
    @title = 'Following'
    @user  = User.find(params[:id])
    @users = @user.following.paginate(page: params[:page])
    render 'show_follow'
  end
```

### Integration Test


### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the follower display"
git push origin 02-08-follower_display
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
