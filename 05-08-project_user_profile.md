# Unit 5
## Chapter 8: Adding Project Display to User Profile

In this chapter, you will add projects to the associated user's profile page.  You will also give users the ability to add projects from their own profile pages.  Please note that visitors who do not log in do not have access to user profile pages and are thus not relevant in this chapter.

### New Branch
Enter the command "git checkout -b 05-08-user_project".

### Integration Test
* Enter the command "rails generate integration_test project_user_profile".
* Edit the file test/integration/project_user_profile_test.rb.  Replace everything between the line "class ProjectUserProfileTest < ActionDispatch::IntegrationTest" and the last end statement with the following:
```
  # rubocop:disable Metrics/AbcSize
  def check_user_pages
    visit user_path(@u2)
    assert_not page.has_css?('h3', text: 'Projects')
    visit user_path(@u3)
    assert page.has_css?('h3', text: 'Projects')
    assert page.has_link?('Live and Let Die', href: project_path(@p1))
    assert page.has_link?('The Man with the Golden Gun', href: project_path(@p2))
    visit user_path(@u5)
    assert page.has_css?('h3', text: 'Projects')
    assert page.has_link?('GoldenEye', href: project_path(@p3))
    assert page.has_link?('Tomorrow Never Dies', href: project_path(@p4))
  end
  # rubocop:enable Metrics/AbcSize

  def check_no_create_button
    visit users_path(@u4)
    assert page.has_no_link?('Add Project', href: projects_path)
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

  test 'correct user can add projects' do
    login_as(@u4, scope: :user)
    visit user_path(@u4)
    click_on 'Add Project'
    assert page.has_css?('title', text: full_title('Add Project'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Add Project')
    fill_in('Title', with: 'The Living Daylights')
    fill_in('Source Code URL', with: 'https://github.com/tdalton/tld')
    fill_in('Deployed URL', with: 'http://www.livingdaylights.com')
    fill_in('Description', with: 'Help Koskov defect from the USSR!')
    click_button('Submit')
    assert_text 'The Living Daylights'
    assert_text 'Help Koskov defect from the USSR!'
    assert page.has_link?('https://github.com/tdalton/tld',
                          href: 'https://github.com/tdalton/tld')
    assert page.has_link?('http://www.livingdaylights.com',
                          href: 'http://www.livingdaylights.com')
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
* Enter the command "test1".  The same 4 tests fail again.  Three tests fail because the user profile page does not display projects.  One test fails because the "Add Project" button is not present.

### User Controller
* Edit the file app/controllers/users_controller.rb.  At the end of the "def show" definition, add the following lines:
```
    @projects = Project.where("user_id=#{@user.id}")
```
* Enter the command "sh testc.sh".  (This step is necessary to make sure that the above change doesn't break the user controller.)  All tests should pass.


### User Profile Page
* Edit the file app/views/users/show.html.erb.  Add the following code after the for hire section:
```
    <% # BEGIN: project display %>
    <% if Project.where("user_id=#{@user.id}").any? %>
      <h3>Projects</h3>
      <ul>
      <% @projects.each do |project| %>
        <li>
        <%= link_to project.title, project_path(project) %>
        <br>
        <% if project.source_code_url.nil? == false %>
          Source Code URL: <%= link_to "#{project.source_code_url}", project.source_code_url %>
          <br>
        <% end %>
        <% if project.deployed_url.nil? == false %>
          Deployed URL: <%= link_to "#{project.deployed_url}", project.deployed_url %>
          <br>
        <% end %>
        <%= project.description[0..140] %>
        </li>
      <% end %>
      </ul>
    <% end %>
    <% # END: project display %>

    <% # BEGIN: add project button %>
    <% if @correct_user == true %>
      <%= link_to "Add Project", new_project_path,
                  class: "btn btn-lg btn-primary" %>
      <br>
    <% end %>
    <% # END: add project button %>
```
* Enter the command "test1".  All test should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.


### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added project display and create button to user profile page"
git push origin 05-08-user_project
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
