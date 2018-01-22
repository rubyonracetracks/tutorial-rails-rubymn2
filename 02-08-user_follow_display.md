# Unit 2
## Chapter 8: Displaying User Relationships

In this chapter, you will update the user profile page to show which users are being followed.  This information is to be available only to that particular user and the admins, NOT to other users or the general public.

The profile page lists the number of other users being followed and a link to another page listing all of these other users.

### New Branch
Enter the command "git checkout -b 02-08-follower_display".

### Part A: Controller Level

#### User Controller Test
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

#### Routing
* Edit the config/routes.rb file.  In the "resources :users" section, add the following lines just after the collection statement:
```
    member do
      get :following
    end
```
* Enter the command "sh testc.sh".  Now the 5 tests fail because the following action is not defined.

#### User Controller
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
* Enter the command "sh testc.sh".  Now 3 tests fail due to a missing template.

#### Follower Page
* Enter the command "touch app/views/users/show_follow.html.erb".
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added follower page (controller level)"
```

### Part B: View Level

#### Updating the Test Fixtures
* Add the following lines to the end of the file test/fixtures/users.yml:
```
bandit:
  last_name: 'Reynolds'
  first_name: 'Burt'
  username: 'breynolds'
  email: 'bo_darville@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'Pontiac Trans Am') %>
  confirmed_at: <%= Time.zone.now %>

snowman:
  last_name: 'Reed'
  first_name: 'Jerry'
  username: 'jreed'
  email: 'cledus_snow@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'The Blocker') %>
  confirmed_at: <%= Time.zone.now %>

frog:
  last_name: 'Sally'
  first_name: 'Field'
  username: 'sfield'
  email: 'carrie@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'runaway bride') %>
  confirmed_at: <%= Time.zone.now %>

justice:
  last_name: 'Gleason'
  first_name: 'Jackie'
  username: 'jgleason'
  email: 'smokey@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'attention getter') %>
  confirmed_at: <%= Time.zone.now %>

junior:
  last_name: 'Henry'
  first_name: 'Mike'
  username: 'mhenry'
  email: 'junior@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'football game') %>
  confirmed_at: <%= Time.zone.now %>

needham:
  last_name: 'Needham'
  first_name: 'Hal'
  username: 'hneedham'
  email: 'director1931@example.com'
  encrypted_password: <%= User.new.send(:password_digest, 'Smokey and the Bandit') %>
  confirmed_at: <%= Time.zone.now %>
```
* Add the following lines to the end of the file test/fixtures/relationships.yml:
```
relationship7:
  follower: needham
  followed: bandit

relationship8:
  follower: needham
  followed: snowman

relationship9:
  follower: needham
  followed: frog

relationship10:
  follower: needham
  followed: justice

relationship11:
  follower: needham
  followed: junior

relationship12:
  follower: justice
  followed: bandit

relationship13:
  follower: junior
  followed: justice
```

#### Test Helper
Update the test/test_helper.rb file.  Add the following lines to the end of the definition of setup_universal:
```
  @u8 = users(:bandit)
  @u9 = users(:snowman)
  @u10 = users(:frog)
  @u11 = users(:justice)
  @u12 = users(:junior)
  @u13 = users(:needham)

  @r1 = relationships(:relationship1)
  @r2 = relationships(:relationship2)
  @r3 = relationships(:relationship3)
  @r4 = relationships(:relationship4)
  @r5 = relationships(:relationship5)
  @r6 = relationships(:relationship6)
  @r7 = relationships(:relationship7)
  @r8 = relationships(:relationship8)
  @r9 = relationships(:relationship9)
  @r10 = relationships(:relationship10)
  @r11 = relationships(:relationship11)
  @r12 = relationships(:relationship12)
  @r13 = relationships(:relationship13)
```

#### Integration Test
* Enter the command "rails generate integration_test user_following".
* Edit the resulting test/integration/user_following_test.rb.  Replace the text between the line "class UserFollowingTest < ActionDispatch::IntegrationTest" and the last "end" line with the following:
```
  # rubocop:disable Metrics/AbcSize
  def can_view_users_following
    assert page.has_link?('breynolds', href: user_path(@u8))
    assert page.has_link?('jreed', href: user_path(@u9))
    assert page.has_link?('sfield', href: user_path(@u10))
    assert page.has_link?('jgleason', href: user_path(@u11))
    assert page.has_link?('mhenry', href: user_path(@u12))
  end
  # rubocop:enable Metrics/AbcSize

  test 'visitors cannot view the user following page' do
    visit following_user_path(@u13)
    assert page.has_css?('title', text: full_title(''),
                                  visible: false)
    assert page.has_css?('h1', text: 'Home', visible: false)
  end

  test "user cannot view another user's following page" do
    login_as(@u12, scope: :user)
    visit following_user_path(@u13)
    assert page.has_css?('title', text: full_title(''),
                                  visible: false)
    assert page.has_css?('h1', text: 'Home', visible: false)
  end

  test "user does not get link to another user's following page" do
    login_as(@u12, scope: :user)
    visit following_user_path(@u13)
    assert_not page.has_text?('Following')
  end

  test 'user following other users can access own following page through home page' do
    login_as(@u13, scope: :user)
    visit root_path
    assert page.has_text?('Following: 5')
    assert page.has_link?('Following: 5', href: following_user_path(@u13))
  end

  test 'user can access own following page through own profile page' do
    login_as(@u13, scope: :user)
    visit root_path
    click_on 'Your Profile'
    assert page.has_link?('Following: 5', href: following_user_path(@u13))
  end

  test 'user can view own list of users followed' do
    login_as(@u13, scope: :user)
    visit following_user_path(@u13)
    assert page.has_css?('h1', text: 'Users You Are Following')
    can_view_users_following
  end

  test 'regular admin can see the list of users followed' do
    login_as(@a4, scope: :admin)
    visit user_path(@u13)
    assert page.has_link?('Following: 5', href: following_user_path(@u13))
    click_on 'Following: 5'
    assert page.has_css?('h1', text: 'Users Followed By Hal Needham')
    can_view_users_following
  end

  test 'super admin can see the list of users followed' do
    login_as(@a1, scope: :admin)
    visit user_path(@u13)
    assert page.has_link?('Following: 5', href: following_user_path(@u13))
    click_on 'Following: 5'
    assert page.has_css?('h1', text: 'Users Followed By Hal Needham')
    can_view_users_following
  end
```
* Enter the command "sh test_app.sh".  The last 5 new integration tests fail.
* Enter the command "alias test1='(Command for running the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same 5 tests fail again.  1 test fails because of a missing heading, and 4 tests fail because the link to the list of followed users is missing.

#### Show/Follow Page
* Give the now-blank app/views/users/show_follow.html.erb file the following content:
```
<% provide(:title, 'Following') %>

<%= gravatar_for @user %>
<br>
<% if current_user == @user %>
  <h1>Users You Are Following:</h1>
<% elsif admin_signed_in? %>
  <h1>Users Followed By <%= link_to "#{@user.first_name} #{@user.last_name}", @user %>:</h1>
<% end %>

<%= render @users %>
<hr>

```
* Enter the command "test1".  Now 4 of the tests still fail.  The show_follow page passes all tests, but the expected links to this page are missing.

#### User Show Page
* Update the file app/views/users/show.html.erb.  Just before the delete button section, add the following code:
```
    <% # BEGIN: link to users followed %>
    <% if current_user == @user || admin_signed_in? %>
      <%= link_to "Following: #{@user.following.count}", following_user_path(@user) %>
      <br>
    <% end %>
    <% # END: link to users followed %>
```
* Enter the command "test1".  Now just one test still fails, because the user does not get a direct link to his/her followed users on the home page.

#### Home Page
* Edit the file app/views/static_pages/home.html.erb. Add the following lines just before the end of the user section:
```
    <br>
    <% if current_user.following.any? %>
      <%= link_to "Following: #{current_user.following.count}", following_user_path(current_user) %>
    <% end %>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should now pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the follower display (view level)"
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
