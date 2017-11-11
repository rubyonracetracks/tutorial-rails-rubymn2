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
    get following_user_path(@u1)
    assert_redirected_to root_path
  end

  test 'should redirect following when logged in as a different user' do
    sign_in @u2, scope: :user
    get following_user_path(@u1)
    assert_redirected_to root_path
  end

  test 'should not redirect following when logged in as that user' do
    sign_in @u1, scope: :user
    get following_user_path(@u1)
    assert_response :success
  end

  test 'should not redirect following when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    get following_user_path(@u1)
    assert_response :success
  end

  test 'should not redirect following when logged in as a super admin' do
    sign_in @a1, scope: :admin
    get following_user_path(@u1)
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
  def can_view_users_following
    assert page.has_link?('Sean Connery', href: user_path(@u1))
    assert page.has_link?('George Lazenby', href: user_path(@u2))
    assert page.has_link?('Roger Moore', href: user_path(@u3))
    assert page.has_link?('Timothy Dalton', href: user_path(@u4))
  end

  test 'visitors cannot view the user following page' do
    visit following_user_path(@u1)
    assert page.has_css?('title', text: full_title(''),
                                  visible: false)
    assert page.has_css?('h1', text: 'Home', visible: false)
  end

  test "user cannot view another user's following page" do
    login_as(@u2, scope: :user)
    visit following_user_path(@u1)
    assert page.has_css?('title', text: full_title(''),
                                  visible: false)
    assert page.has_css?('h1', text: 'Home', visible: false)
  end

  test "user does not get link to another user's following page" do
    login_as(@u2, scope: :user)
    visit following_user_path(@u1)
    assert_not page.has_text('Following', href: following_user_path(@u1))
  end

  test 'user following other users can access own following page through home page' do
    login_as(@u5, scope: :user)
    visit root_path
    click_on 'Your Profile'
    assert page.has_text('Following: 4')
    assert page.has_link?('Following: 4', href: following_user_path(@u5))
  end

  test 'user can access own following page through own profile page' do
    login_as(@u5, scope: :user)
    visit root_path
    click_on 'Your Profile'
    assert page.has_link?('Following: 4', href: following_user_path(@u5))
  end

  test 'user can view own list of users followed' do
    login_as(@u5, scope: :user)
    visit following_user_path(@u5)
    assert page.has_css?('h1', text: 'Users You Are Following')
    can_view_users_following
  end

  test 'regular admin can see the list of users followed' do
    login_as(@a4, scope: :admin)
    visit user_path(@u5)
    assert page.has_link?('Following: 4', href: following_user_path(@u5))
    click_on 'Following: 4'
    assert page.has_css?('h1', text: 'Users Followed By Pierce Brosnan')
    can_view_users_following
  end

  test 'super admin can see the list of users followed' do
    login_as(@a1, scope: :admin)
    visit user_path(@u5)
    assert page.has_link?('Following: 4', href: following_user_path(@u5))
    click_on 'Following: 4'
    assert page.has_css?('h1', text: 'Users Followed By Pierce Brosnan')
    can_view_users_following
  end
```
* Enter the command "sh test_app.sh".  The last 4 new integration tests fail.
* Enter the command "alias test1='(Command for running the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same 4 tests fail again because the expected content is missing.
* Edit the file test/fixtures/relationships.yml.  Replace the entire contents with the following:
```
one:
  follower: brosnan
  followed: connery

two:
  follower: brosnan
  followed: lazenby

three:
  follower: brosnan
  followed: moore

four:
  follower: brosnan
  followed: dalton

five:
  follower: connery
  followed: blofeld
```
* Give the now-blank app/views/users/show_follow.html.erb file the following content:
```
<% provide(:title, @title) %>
<div class="row">
  <aside class="col-md-4">
    <section class="user_info">
      <%= gravatar_for @user %>
      <h1><%= @user.name %></h1>
      <span><%= link_to "view my profile", @user %></span>
      <span><b>Microposts:</b> <%= @user.microposts.count %></span>
    </section>
    <section class="stats">
      <%= render 'shared/stats' %>
      <% if @users.any? %>
        <div class="user_avatars">
          <% @users.each do |user| %>
            <%= link_to gravatar_for(user, size: 30), user %>
          <% end %>
        </div>
      <% end %>
    </section>
  </aside>
  <div class="col-md-8">
    <h3><%= @title %></h3>
    <% if @users.any? %>
      <ul class="users follow">
        <%= render @users %>
      </ul>
      <%= will_paginate %>
    <% end %>
  </div>
</div>
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
