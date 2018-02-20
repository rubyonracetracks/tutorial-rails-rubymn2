# Unit 5
## Chapter 2: Project Show Method
In this chapter, you will add the show method for the project object.

### New Branch
Enter the command "git checkout -b 05-02-project_show".

### Part A: Controller Level

### Controller Testing
* Enter the command "rails generate controller Projects".
* Enter the command "rm app/helpers/projects_helper.rb".
* Edit the file test/controllers/projects_controller_test.rb.  Replace everything between the line "class ProjectsControllerTest < ActionDispatch::IntegrationTest" and the last "end" command with the following:
```
  test 'project show action' do
    get project_path(@p1)
    assert_response :success
    get project_path(@p2)
    assert_response :success
    get project_path(@p3)
    assert_response :success
    get project_path(@p4)
    assert_response :success
    get project_path(@p5)
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  The new controller test fails because project_path is undefined.

#### Routing
* Edit the config/routes.rb file.  Just before the last "end" statement, add the following line:
```
  # BEGIN: project section
  resources :projects, only: [:show]
  # END: project section
```
* Enter the command "sh testc.sh".  The new controller test fails because of missing routes.  (The project objects are not yet provided.)

#### Test Helper
* In the file test/setup_objects.rb, add the following lines just before the end of the definition of add_user_objects:
```
  @p1 = @u3.projects.create(title: 'Live and Let Die',
                            description: 'Investigate Kananga.',
                            source_code_url: 'https://github.com/rmoore/kananga',
                            deployed_url: 'http://www.kananga.com')
  @p2 = @u3.projects.create(title: 'The Man with the Golden Gun',
                            description: 'Get the Solex Agitator from Scaramanga.',
                            source_code_url: 'https://github.com/rmoore/scaramanga',
                            deployed_url: 'http://www.scaramanga.com')
  @p3 = @u5.projects.create(title: 'GoldenEye',
                            description: 'Stop the hijacking of the GoldenEye satellite.',
                            source_code_url: 'https://github.com/pbrosnan/goldeneye',
                            deployed_url: 'http://www.goldeneye.com')
  @p4 = @u5.projects.create(title: 'Tomorrow Never Dies',
                            description: 'Prevent war between China and the UK.',
                            source_code_url: 'https://github.com/pbrosnan/carver',
                            deployed_url: 'http://www.carvernewsnetwork.com')
  @p5 = @u6.projects.create(title: 'Quantum of Solace',
                            description: 'Get revenge for the death of Vesper Lynd.')
```
* Enter the command "sh testc.sh".  Now the test fails because the "show" action is not provided in the project controller.

#### Controller
* Edit the file app/controllers/projects_controller.rb.  Add the line "#" immediately before the line "class ProjectsController < ApplicationController".
* Edit the file app/controllers/projects_controller.rb. Insert the following lines between "class ProjectsController < ApplicationController" and "end":
```
  # BEGIN: action section
  def show
    @project = Project.find(params[:id])
    @user = User.where("id=#{@project.user_id}").first
  end
  # END: action section
```
* Enter the command "sh testc.sh". The controller test fails because of a missing template.
* Enter the command "touch app/views/projects/show.html.erb" to provide the template. (You'll add content to it later.)
* Enter the command "sh testc.sh". All tests should now pass.
* Enter the command "sh git_check.sh". All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the project show capability (controller level)"
```

### Part B: View Level

#### Integration Testing
* Enter the command "rails generate integration_test project_show".
* Edit the file test/integration/project_show_test.rb.  Replace everything between the line "class ProjectShowTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def check_project_pages
    visit project_path(@p1)
    assert page.has_css?('title',
                         text: full_title('Project: Live and Let Die'),
                         visible: false)
    assert page.has_css?('h1', text: 'Project: Live and Let Die')
    assert_text 'Investigate Kananga.'
    assert_text 'Source Code URL:'
    assert_text 'Deployed URL:'
    assert page.has_link?('https://github.com/rmoore/kananga',
                           href: 'https://github.com/rmoore/kananga')
    assert page.has_link?('http://www.kananga.com',
                           href: 'http://www.kananga.com')

    visit project_path(@p2)
    assert page.has_css?('title',
                         text: full_title('The Man with the Golden Gun'),
                         visible: false)
    assert page.has_css?('h1', text: 'Project: The Man with the Golden Gun')
    assert_text 'Get the Solex Agitator from Scaramanga.'
    assert_text 'Source Code URL:'
    assert_text 'Deployed URL:'
    assert page.has_link?('https://github.com/rmoore/scaramanga',
                           href: 'https://github.com/rmoore/scaramanga')
    assert page.has_link?('http://www.scaramanga.com',
                           href: 'http://www.scaramanga.com')

    visit project_path(@p3)
    assert page.has_css?('title',
                         text: full_title('GoldenEye'),
                         visible: false)
    assert page.has_css?('h1', text: 'Project: GoldenEye')
    assert_text 'Stop the hijacking of the GoldenEye satellite.'
    assert_text 'Source Code URL:'
    assert_text 'Deployed URL:'
    assert page.has_link?('ttps://github.com/pbrosnan/goldeneye',
                           href: 'ttps://github.com/pbrosnan/goldeneye')
    assert page.has_link?('http://www.goldeneye.com',
                           href: 'http://www.goldeneye.com')

    visit project_path(@p4)
    assert page.has_css?('title',
                         text: full_title('Project: Tomorrow Never Dies'),
                         visible: false)
    assert page.has_css?('h1', text: 'Project: Tomorrow Never Dies')
    assert_text 'Prevent war between China and the UK.'
    assert_text 'Source Code URL:'
    assert_text 'Deployed URL:'
    assert page.has_link?('https://github.com/pbrosnan/carver',
                           href: 'https://github.com/pbrosnan/carver')
    assert page.has_link?('http://www.carvernewsnetwork.com',
                           href: 'http://www.carvernewsnetwork.com')

    visit project_path(@p5)
    assert page.has_css?('title',
                         text: full_title('Project: Quantum of Solace'),
                         visible: false)
    assert page.has_css?('h1', text: 'Project: Quantum of Solace')
    assert_text 'Get revenge for the death of Vesper Lynd.'
    assert_not page.has_text?('Source Code URL:')
    assert_not page.has_text?('Deployed URL:')
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

  def check_project_for_link
    visit project_path(@p1)
    assert page.has_link?('Roger Moore', href: user_path(@u3))
    visit project_path(@p2)
    assert page.has_link?('Roger Moore', href: user_path(@u3))
    visit project_path(@p3)
    assert page.has_link?('Pierce Brosnan', href: user_path(@u5))
    visit project_path(@p4)
    assert page.has_link?('Pierce Brosnan', href: user_path(@u5))
    visit project_path(@p5)
    assert page.has_link?('Daniel Craig', href: user_path(@u6))
  end

  test 'visitor sees the expected content on pages' do
    check_project_pages
    visit project_path(@p1)
    assert_not page.has_link?('Roger Moore', href: user_path(@u3))
    visit project_path(@p2)
    assert_not page.has_link?('Roger Moore', href: user_path(@u3))
    visit project_path(@p3)
    assert_not page.has_link?('Pierce Brosnan', href: user_path(@u5))
    visit project_path(@p4)
    assert_not page.has_link?('Pierce Brosnan', href: user_path(@u5))
    visit project_path(@p5)
    assert_not page.has_link?('Daniel Craig', href: user_path(@u6))
  end

  test 'user sees the expected content on pages' do
    login_as(@u1, scope: :user)
    check_project_pages
    check_project_for_link
  end

  test 'regular admin sees the expected content on pages' do
    login_as(@a4, scope: :admin)
    check_project_pages
    check_project_for_link
  end

  test 'super admin sees the expected content on pages' do
    login_as(@a1, scope: :admin)
    check_project_pages
    check_project_for_link
  end
```
* Please note that unregistered visitors do not get a link to the user profile page, because they do not have access to it.
* Enter the command "sh test_app.sh".  All 4 new integration tests fail.
* Enter the command "alias test1='Command to run failed tests minus the TESTOPTS portion'".
* Enter the command "test1".  The same 4 integration tests fail because the project profile page does not have the expected content.

#### Project Profile Page
* Fill in the blank app/views/projects/show.html.erb file with the following code:
```
<% provide(:title, "Project: #{@project.title}") %>

<h1>Project: #{@project.title}</h1>

<% # BEGIN: name and title of user %>
<% if admin_signed_in? || user_signed_in? %>
  Author: <%= link_to "#{@user.first_name} #{@user.last_name}", @user %>
<% else %>
  Author: <%= @user.first_name %> <%= @user.last_name %>
<% end %>
<% # END: name and title of user %>
<br><br>
Source Code URL: <%= @project.source_code_url %>
<br><br>
Deployed URL: <%= @project.deployed_url %>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the project show capability (view level)"
git push origin 05-02-project_show
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
