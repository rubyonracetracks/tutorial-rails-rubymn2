# Unit 5
## Chapter 5: Project Create

In this chapter, you will give users the ability to create a project profile.

### New Branch
Enter the command "git checkout -b 05-05-project_create".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/projects_controller_test.rb.  Add the following code immediately after the line "class ProjectsControllerTest < ActionDispatch::IntegrationTest":
```
  # BEGIN: definitions
  def create_project
    title1 = 'Fake Blofelds'
    url_s = 'https://github.com/blofeld/fake_blofelds'
    url_d = 'http://www.fakeblofelds.com'
    desc1 = 'Create fake versions of myself to confuse James Bond!'
    post projects_path, params: { project: { title: title1,
                                             source_code_url: url_s,
                                             deployed_url: url_d,
                                             description: desc1 } }
  end

  def create_project_disabled
    assert_no_difference 'Project.count' do
      create_project
    end
    assert_redirected_to root_path
  end
  # END: definitions
```
* Edit the file test/controllers/projects_controller_test.rb.  Add the following code immediately before the last "end" statement:
```
  test 'should redirect create when not logged in' do
    create_project_disabled
  end

  test 'should not redirect user' do
    sign_in @u7, scope: :user
    assert_difference 'Project.count', 1 do
      create_project
    end
    assert_redirected_to user_path(@u7)
  end

  test 'should redirect create for regular admin' do
    sign_in @a4, scope: :admin
    create_project_disabled
  end

  test 'should redirect create for super admin' do
    sign_in @a1, scope: :admin
    create_project_disabled
  end
```
* Enter the command "sh testc.sh".  All 5 new tests fail because the expected routing is not provided.

#### Routing
* Edit the file config/routes.rb.  Replace the line that begins with "resources :projects" with the following:
```
  resources :projects, only: [:show, :index, :create, :new] do
```
* Enter the command "sh testc.sh".  The 5 tests fail because the create action could not be found..

#### Controller
* Edit the file app/controllers/projects_controller.rb.  Immediately after the line "class ProjectsController < ApplicationController", add the following code:
```
  # BEGIN: before_action section
  before_action :may_create_project, only: [:create]
  # END: before_action section
```
* Edit the file app/controllers/projects_controller.rb.  Immediately before the last "end" statement, add the following code:
```
  # BEGIN: private section
  # rubocop:disable Metrics/LineLength
  def may_create_project
    return redirect_to(root_path) unless user_signed_in?
  end
  helper_method :may_create_project
  # rubocop:enable Metrics/LineLength

  def project_params
    params.require(:project).permit(:title, :source_code_url, :deployed_url, :description)
  end
  # END: private section
```
* Edit the file app/controllers/projects_controller.rb.  Add the following code just before the end of the action section:
```
  def create
    @project = current_user.projects.build(project_params)
    if @project.save
      flash[:info] = 'Project added'
      redirect_to user_path(current_user)
    else
      render 'new'
    end
  end

  def new
    @project = Project.new
  end
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the project create capability (controller level)"
```

### Part B: View Level

Please note that a user who successfully adds a project is redirected to his/her profile page.  In a later chapter, you will add the project and the "Add Project" button on the user profile page.  In this chapter, the focus is on providing the working "Add Project" button on the project index page.

#### Integration Test
* Enter the command "rails generate integration_test project_create".
* Edit the file test/integration/project_create_test.rb.  Replace everything between the line "class ProjectCreateTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def check_no_create_button
    visit projects_path
    assert page.has_no_link?('Add Project', href: projects_path)
  end

  test 'visitor does not get button to add project' do
    check_no_create_button
  end

  test 'user gets button to add project' do
    login_as(@u7, scope: :user)
    visit projects_path
    assert page.has_link?('Add Project', href: new_project_path)
  end

  test 'regular admin does not get button to add project' do
    login_as(@a4, scope: :admin)
    check_no_create_button
  end

  test 'super admin does not get button to add project' do
    login_as(@a1, scope: :admin)
    check_no_create_button
  end

  test 'user can successfully add project' do
    login_as(@u13, scope: :user)
    visit projects_path

    click_on 'Add Project'
    assert page.has_css?('title', text: full_title('Add Project'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Add Project')
    fill_in('Title', with: 'Smokey and the Bandit')
    fill_in('Source Code URL', with: 'https://github.com/hneedham/smokey_and_the_bandit')
    fill_in('Deployed URL', with: 'http://www.smokeyandthebandit.com')
    fill_in('Description', with: 'total lack of respect for the law')
    click_button('Submit')

    assert page.has_css?('title', text: full_title('User: Hal Needham'),
                                  visible: false)
    assert page.has_css?('h1', text: 'User: Hal Needham')

    click_on 'Home'
    click_on 'Projects'
    assert page.has_css?('title', text: full_title('Project Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Project Index')
    assert_text 'Smokey and the Bandit'
    assert_text 'total lack of respect for the law'
    assert page.has_link?('https://github.com/hneedham/smokey_and_the_bandit',
                          href: 'https://github.com/hneedham/smokey_and_the_bandit')    
    assert page.has_link?('http://www.smokeyandthebandit.com',
                          href: 'http://www.smokeyandthebandit.com')
  end
```
* Enter the command "sh test_app.sh".  Two of the new integration tests fail.
* Enter the command "alias test1='(command to run the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same two integration tests fail.  Both tests fail because the link to add a new project is missing.

#### Project Index
* Edit the file app/views/projects/index.html.erb. Immediately after the listing of number of projects, add the following code:
```
<% # BEGIN: add project button %>
<% if user_signed_in? %>
  <%= link_to "Add Project", new_project_path,
              class: "btn btn-lg btn-primary"
    %>
    <br>
<% end %>
<% # END: add project button %>
```
* Enter the command "test1".  One test fails because the form for entering a project is absent.

#### Form For Adding Project
* Enter the command "touch app/views/projects/new.html.erb".  
* Enter the command "test1".  Now the test fails because the page for entering parameters for the project is blank.
* Give the blank file app/views/projects/new.html.erb the following content:
```
<% provide(:title, 'Add Project') %>

<h1>Add Project</h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(@project) do |f| %>
      <%= render 'shared/error_messages', object: f.object %>

      <div class="field">
        <%= f.label :title %><br />
        <%= f.text_field :title %>
      </div>

      <div class="field">
        <%= f.label :source_code_url %><br />
        <%= f.text_field :source_code_url %>
      </div>

      <div class="field">
        <%= f.label :deployed_url %><br />
        <%= f.text_field :deployed_url %>
      </div>

      <div class="field">
        <%= f.label :description, 'Background Statement' %><br />
        <%= f.text_area :description, placeholder: "Enter background statement here...", rows: 15 %>
      </div>

      <%= f.submit "Submit", class: "btn btn-primary" %>
    <% end %>
  </div>
</div>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the project create capability (view level)"
git push origin 05-05-project_create
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh". 
