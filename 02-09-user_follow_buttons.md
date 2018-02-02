# Unit 2
## Chapter 9: User Relationship Buttons

In this chapter, you will add buttons that allow users to follow or unfollow other users.

### New Branch
Enter the command "git checkout -b 02-09-follower_buttons".

### Controller Test
* Enter the command "rails generate controller Relationships".
* Edit the file test/controllers/relationships_controller_test.rb.  Replace everything between the line "class RelationshipsControllerTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  # NOTE: I was unable to create suitable controller tests of the
  # relationship creation process.  The relationship creation process
  # is tested in the integration tests.

  def destroy_relationship
    delete relationship_path(@r8)
  end

  def destroy_relationship_disabled
    assert_no_difference 'Relationship.count' do
      destroy_relationship
    end
  end

  test 'visitor cannot destroy relationship' do
    destroy_relationship_disabled
  end

  test 'the wrong user cannot destroy relationship' do
    sign_in @u1, scope: :user
    destroy_relationship_disabled
    sign_in @u8, scope: :user
    destroy_relationship_disabled
  end

  test 'the correct user can destroy relationship' do
    sign_in @u13, scope: :user
    assert_difference 'Relationship.count', -1 do
      destroy_relationship
    end
    assert_redirected_to user_path(@u9)
  end

  test 'regular admin cannot destroy relationship' do
    sign_in @a4, scope: :admin
    destroy_relationship_disabled
  end

  test 'super admin cannot destroy relationship' do
    sign_in @a1, scope: :admin
    destroy_relationship_disabled
  end
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because relationships_path is not defined.
* Edit the file config/routes.rb.  Add the following lines just before the final "end" command:
```
  resources :relationships, only: [:create, :destroy]
```
* Enter the command "sh testc.sh".  The same 5 controller tests fail because the destroy action is not defined.

* Edit the app/controllers/relationships_controller.rb file.  Between the line "class RelationshipsController < ApplicationController" and "end", insert the following lines:
```
  # NOTE: Checking to see if the user is the right user is not a viable
  # way to proceed.  The authenticate_user command is used instead.
  before_action :authenticate_user!

  def create
    @user2 = User.find(params[:followed_id])
    current_user.follow(@user2)
    redirect_to(user_path(@user2))
  end

  def destroy
    @user2 = Relationship.find(params[:id]).followed
    current_user.unfollow(@user2)
    redirect_to user_path(@user2)
  end
```
* Enter the command "sh testc.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, but there will be Rubocop offenses.
* Enter the command "rm app/helpers/relationships_helper.rb".
* In the file app/controllers/relationships_controller.rb, add the line "#" just before the line "class RelationshipsController < ApplicationController".
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Integration Test
* Enter the command "rails generate integration_test following_button".
* In the file test/integration/following_button_test.rb, replace all lines between "class FollowingButtonTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  # NOTE: Visitors cannot access the user profile page (where the Follow/
  # Unfollow button is located).

  def no_follow_unfollow_button
    visit user_path(@u1)
    assert page.has_no_link?('Follow', href: user_path(@u1))
    assert page.has_no_link?('Unfollow', href: user_path(@u1))
  end

  test 'regular admin does not see the follow or unfollow buttons' do
    login_as(@a4, scope: :admin)
    no_follow_unfollow_button
  end

  test 'super admin does not see the follow or unfollow buttons' do
    login_as(@a1, scope: :admin)
    no_follow_unfollow_button
  end

  test 'user does not see the follow or unfollow buttons on own page' do
    login_as(@u1, scope: :user)
    no_follow_unfollow_button
  end

  test 'user can use the follow and unfollow buttons' do
    assert_difference '@u7.following.count', 1 do
      login_as(@u7, scope: :user)
      visit user_path(@u1)
      click_on 'Follow'
    end
    assert_difference '@u7.following.count', -1 do
      click_on 'Unfollow'
    end
    assert_difference '@u7.following.count', 1 do
      click_on 'Follow'
    end
  end
```
* Enter the command "sh test_app.sh".  The last new integration test fails.
* Enter the command "alias test1='(Command to run failed test minus the TESTOPTS portion)'".
* Enter the command "test1".  The test fails because the follow/unfollow button is missing.
* Edit the app/views/users/show.html.erb file.  Add the following code just before the delete button:
```
    <% # BEGIN: follow/unfollow button %>
    <% if user_signed_in? && current_user != @user %>
      <% if current_user.following?(@user) %>
        <%= form_for(current_user.active_relationships.build, remote: true) do |f| %>
          <div><%= hidden_field_tag :followed_id, @user.id %></div>
          <%= f.submit "Follow", class: "btn btn-primary" %>
        <% end %>
        <br>
      <% else %>
        <%= form_for(current_user.active_relationships.find_by(followed_id: @user.id),
                     html: { method: :delete },
                     remote: true) do |f| %>
          <%= f.submit "Unfollow", class: "btn" %>
        <% end %>
        <br>
      <% end %>
    <% end %>
    <% # END: follow/unfollow button %>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the follow/unfollow buttons"
git push origin 02-09-follower_buttons
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
