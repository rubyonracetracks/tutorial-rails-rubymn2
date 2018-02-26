# Unit 5
## Chapter 8: Adding Job Opening Display to User Profile

In this chapter, you will add openings to the associated user's profile page.  You will also give users the ability to add openings from their own profile pages.  Please note that visitors who do not log in do not have access to user profile pages and are thus not relevant in this chapter.

### New Branch
Enter the command "git checkout -b 06-08-user_opening".

### Integration Test
* Enter the command "rails generate integration_test opening_user_profile".
* Edit the file test/integration/opening_user_profile_test.rb.  Replace everything between the line "class OpeningUserProfileTest < ActionDispatch::IntegrationTest" and the last end statement with the following:
```
  # rubocop:disable Metrics/AbcSize
  def check_user_pages
    visit user_path(@u2)
    assert_not page.has_css?('h3', text: 'Job Openings')

    visit user_path(@u7)
    assert page.has_css?('h3', text: 'Job Openings')
    assert page.has_link?('Spacecraft Hijacker', href: opening_path(@op1))
    assert page.has_link?('Head of Unione Corse', href: opening_path(@op2))
    assert page.has_link?('Plastic Surgeon', href: opening_path(@op3))
    assert_text 'Hijack American and Soviet spacecraft'
    assert_text 'Run a European crime syndicate'
    assert_text 'Create fake versions of Blofeld to foil 007'

    visit user_path(@u11)
    assert page.has_css?('h3', text: 'Job Openings')
    assert page.has_link?('Deputy', href: opening_path(@op4))
    assert page.has_link?('Body Repair Technician', href: opening_path(@op5))
    assert_text 'Catch that black Trans Am!'
    assert_text 'Fix those police cars I keep wrecking'
  end
  # rubocop:enable Metrics/AbcSize

  def check_no_create_button
    visit users_path(@u4)
    assert page.has_no_link?('Add Job Opening', href: openings_path)
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

  test 'correct user can add openings' do
    login_as(@u5, scope: :user)
    visit user_path(@u5)
    click_on 'Add Job Opening'
    assert page.has_css?('title', text: full_title('Add Job Opening'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Add Job Opening')
    fill_in('Title', with: 'Intern')
    fill_in('Description', with: 'Support my volcanology research')
    click_button('Submit')
    assert text 'Intern'
    assert_text 'Support my volcanology research'
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
```
* Enter the command "sh test_app.sh".  4 tests fail.
* Enter the command "alias test1='Command to run failed tests minus the TESTOPTS portion'".
* Enter the command "test1".  The same 4 tests fail again.  Three tests fail because the user profile page does not display openings.  One test fails because the "Add Job Opening" button is not present.

### User Controller
* Edit the file app/controllers/users_controller.rb.  At the end of the "def show" definition, add the following lines:
```
    @openings = Opening.where("user_id=#{@user.id}")
```
* Enter the command "sh testc.sh".  (This step is necessary to make sure that the above change doesn't break the user controller.)  All tests should pass.


### User Profile Page
* Edit the file app/views/users/show.html.erb.  Add the following code after the for hire section:
```
    <% # BEGIN: opening display %>
    <% if Opening.where("user_id=#{@user.id}").any? %>
      <h3>Job Openings</h3>
      <ul>
      <% @openings.each do |opening| %>
        <li>
        <%= link_to opening.title, opening_path(opening) %>
        <br>
        <%= opening.description[0..140] %>
        </li>
      <% end %>
      </ul>
    <% end %>
    <% # END: opening display %>

    <% # BEGIN: add opening button %>
    <% if @correct_user == true %>
      <%= link_to "Add Job Opening", new_opening_path,
                  class: "btn btn-lg btn-primary" %>
      <br>
    <% end %>
    <% # END: add opening button %>
```
* Enter the command "test1".  All test should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.


### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added opening display and create button to user profile page"
git push origin 06-08-user_opening
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```

### Congratulations!

You have completed this tutorial.  Just remember to update the Ruby version and gem versions used in all of your apps.
