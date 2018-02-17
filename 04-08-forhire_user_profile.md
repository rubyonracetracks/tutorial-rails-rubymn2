# Unit 4
## Chapter 8: Adding Forhire Display to User Profile

In this chapter, you will add the for hire profile to the associated user's profile page.  You will also give users the ability to add a for hire profile (if they don't already have one) from their own profile pages.  Please note that visitors who do not log in do not have access to user profile pages and are thus not relevant in this chapter.

### New Branch
Enter the command "git checkout -b 04-08-user_forhire".

### Integration Test
* Enter the command "rails generate integration_test forhire_user_profile".
* Edit the file test/integration/forhire_user_profile_test.rb.  Replace everything between the line "class ForhireUserProfileTest < ActionDispatch::IntegrationTest" and the last end statement with the following:
```
  def check_user_pages
    visit user_path(@u1)
    assert page.has_link?('James Bond 1962-1971', href: forhire_path(@fh_connery))
    visit user_path(@u2)
    assert page.has_link?('James Bond 1969', href: forhire_path(@fh_lazenby))
  end

  def check_no_create_button
    visit users_path(@u8)
    assert page.has_no_link?('Add For Hire Profile', href: forhires_path)
  end

  test 'user sees the expected content on pages' do
    login_as(@u1, scope: :user)
    check_user_pages
  end

  test 'regular admin sees the expected content on pages' do
    login_as(@a4, scope: :admin)
    check_user_pages
  end

  test 'super admin sees the expected content on pages' do
    login_as(@a1, scope: :admin)
    check_user_pages
  end

  test 'correct user without for hire can create for hire profile' do
    login_as(@u8, scope: :user)
    visit user_path(@u8)
    click_on 'Add For Hire Profile'
    assert page.has_css?('title', text: full_title('Add Your For Hire Profile'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Add Your For Hire Profile')
    fill_in('Title', with: 'Bandit')
    fill_in('Email', with: 'bandit@rubyonracetracks.com')
    fill_in('Background Statement', with: 'I can smuggle Coors Beer east bound and down!')
    click_button('Submit')
    assert page.has_css?('h3', text: 'Profile')
    assert_text 'I can smuggle Coors Beer east bound and down!'
  end

  test 'wrong user does not get create button' do
    login_as(@u1, scope: :user)
    check_no_create_button
  end

  test 'regular admin does not get create button' do
    login_as(@a4, scope: :admin)
    check_no_create_button
  end

  test 'super admin does not get create button' do
    login_as(@a1, scope: :admin)
    check_no_create_button
  end

  test 'user with for hire profile does not get create button' do
    login_as(@u1, scope: :user)
    check_no_create_button
  end
```
* Enter the command "sh test_app.sh".  4 tests fail.
* Enter the command "alias test1='Command to run failed tests minus the TESTOPTS portion'".
* Enter the command "test1".  The same 4 tests fail again.  Three tests fail because the user profile page does not display the for hire profile.  One test fails because the "Add For Hire Profile" button is not present.

### User Controller
* Edit the file app/controllers/users_controller.rb.  At the end of the "def show" definition, add the following lines:
```
    @forhire = Forhire.where("user_id=#{@user.id}").first
    @correct_user = correct_user
```
* Edit the file app/controllers/users_controller.rb.  Immediately after the defintion of correct_user, add the following line:
```
  helper_method :correct_user
```
* Enter the command "sh testc.sh".  (This step is necessary to make sure that the above change doesn't break the user controller.)  All tests should pass.


### User Profile Page
* Edit the file app/views/users/show.html.erb.  Add the following code after the delete button section:
```
    <% # BEGIN: forhire display %>
    <% if Forhire.where("user_id=#{@user.id}").any? %>
      <h3>Profile</h3>
      Title: <%= link_to @forhire.title, forhire_path(@forhire) %>
      <br>
      <%= @forhire.description[0..140] %>
      <br>
    <% end %>
    <% # END: forhire display %>

    <% # BEGIN: add forhire button %>
    <% if @correct_user == true && Forhire.where("user_id=#{@user.id}").empty? %>
      <%= link_to "Add For Hire Profile", new_forhire_path,
                  class: "btn btn-lg btn-primary" %>
      <br>
    <% end %>
    <% # END: add forhire button %>

```
* Enter the command "test1".  All test should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.


### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added forhire display and create button to user profile page"
git push origin 04-08-user_forhire
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
