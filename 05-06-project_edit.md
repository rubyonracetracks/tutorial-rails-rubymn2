# Unit 5
## Chapter 6: Project Edit

### New Branch
Enter the command "git checkout -b 05-06-project_edit".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/projects_controller_test.rb. Add the following code immediately before the end of the definition section:
```
  def edit_project
    title1 = 'For Your Eyes Only'
    desc1 = 'Retrieve ATAC'
    url_s = 'https://github.com/rmoore/fyeo'
    url_d = 'http://www.fyeo1981.com'
    patch project_path(@p2),
          params: { project: { title: title1,
                               description: desc1,
                               source_code_url: url_s,
                               deployed_url: url_d } }
  end

  def edit_project_disabled
    edit_project
    assert_redirected_to root_path
  end
```
* Edit the file test/controllers/projects_controller_test.rb.  Add the following code just before the last "end" statement:
```
  test 'should redirect edit to root when not logged in' do
    edit_project_disabled
  end

  # rubocop:disable Metrics/LineLength
  test 'should not redirect edit to root when logged in as the user with the project' do
    sign_in @u3, scope: :user
    edit_project
    assert_redirected_to project_path(@p2)
  end
  # rubocop:enable Metrics/LineLength

  test 'should redirect edit to root when logged in as a different user' do
    sign_in @u2, scope: :user
    edit_project_disabled
  end

  test 'should redirect edit to root when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    edit_project_disabled
  end

  test 'should redirect edit to root when logged in as a super admin' do
    sign_in @a1, scope: :admin
    edit_project_disabled
  end
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because of a missing route.

#### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :projects" with the following:
```
  resources :projects, only: [:show, :index, :create, :new, :update, :edit] do
```
* Enter the command "sh testc.sh".  The same 5 tests fail because the update action is not provided in the project controller.

#### Project Controller
* Just before the end of the before_action section in app/controllers/projects_controller.rb, add the following code:
```
  before_action :may_edit_project, only: [:update]
```
* Just before the end of the action section in app/controllers/projects_controller.rb, add the following code:
```
  def update
    @project = Project.find(params[:id])
    if @project.update_attributes(project_params)
      flash[:success] = 'Project updated'
      redirect_to project_path(@project)
    else
      render 'edit'
    end
  end

  def edit
    @project = Project.find(params[:id])
  end
```
* Just before the line "def project_params" in app/controllers/projects_controller.rb, add the following code:
```
  def may_edit_project
    return redirect_to root_url unless user_signed_in?
    @project = current_user.projects.find_by(id: params[:id])
    return redirect_to root_url if @project.nil?
  end
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added project edit (controller level)"
```

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test project_edit".
* In the file test/integration/project_edit_test.rb, replace everything between the line "class ProjectEditTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def check_no_edit_button
    visit project_path(@p2)
    assert page.has_no_link?('Edit Project', href: project_path(@p2))
  end

  test 'visitor may not edit project' do
    check_no_edit_button
  end

  test 'wrong user may not edit project' do
    login_as(@u2, scope: :user)
    check_no_edit_button
  end

  test 'correct user gets button to edit project' do
    login_as(@u3, scope: :user)
    visit project_path(@p2)
    assert page.has_link?('Edit Project', href: edit_project_path(@p2))
  end

  test 'regular admin may not edit project' do
    login_as(@a4, scope: :admin)
    check_no_edit_button
  end

  test 'super admin may not edit project' do
    login_as(@a1, scope: :admin)
    check_no_edit_button
  end

  test 'correct user can successfully edit project' do
    login_as(@u3, scope: :user)
    visit edit_project_path(@p2)

    assert page.has_css?('title', text: full_title('Edit Project'),
                                  visible: false)
    fill_in('Title', with: 'A View To A Kill')
    fill_in('Source Code URL', with: 'https://github.com/rmoore/avtak')
    fill_in('Deployed URL', with: 'http://www.aviewtoakill.com')
    fill_in('Description', with: 'Investigate Zorin Industries')
    click_button('Submit')

    assert page.has_css?('title',
                         text: full_title('Project: A View To A Kill'),
                         visible: false)
    assert page.has_css?('h1', text: 'Project: A View To A Kill')
    assert_text 'Investigate Zorin Industries'
    assert page.has_link?('https://github.com/rmoore/avtak',
                          href: 'https://github.com/rmoore/avtak')
    assert page.has_link?('http://www.aviewtoakill.com',
                          href: 'http://www.aviewtoakill.comm')
  end
```
  * Enter the command "sh test_app.sh".  Two of the new integration tests fail.
  * Enter the command "alias test1='command for running failed tests minus the TESTOPTS portion'".
  * Enter the command "test1".  The same two integration tests fail.  One test fails because the correct user does not get the expected edit button.  The other test fails because of a missing template.

#### Edit Project Button  
* Edit the file app/views/projects/show.html.erb. Immediately after the h1 heading near the top of the page, add the following code:
```
<% # BEGIN: edit project button %>
<% if user_signed_in? && current_user == @user %>
  <%= link_to "Edit Project", edit_project_path(@project),
              class: "btn btn-lg btn-primary"
    %>
  <br><br>
<% end %>
<% # END: edit project button %>
```
* Enter the command "test1".  Now only one test fails, and it's because of a missing template.

#### Edit Project Form
* Enter the command "touch app/views/projects/edit.html.erb".
* Enter the command "test1".  Now the test fails because the project edit page does not have the expected content.
* Give the file app/views/projects/edit.html.erb the following content:
```
<% provide(:title, 'Edit Project') %>

<h1>Edit Project</h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(@project) do |f| %>
      <%= render 'shared/error_messages', object: f.object %>

      <div class="field">
        <%= f.label :title %><br />
        <%= f.text_field :title %>
      </div>

      <div class="field">
        <%= f.label :source_code_url, 'Source Code URL' %><br />
        <%= f.text_field :source_code_url %>
      </div>

      <div class="field">
        <%= f.label :deployed_url, 'Deployed URL' %><br />
        <%= f.text_field :deployed_url %>
      </div>

      <div class="field">
        <%= f.label :description %><br />
        <%= f.text_area :description, placeholder: "Enter background statement here...", rows: 15 %>
      </div>

      <%= f.submit "Submit", class: "btn btn-primary" %>
    <% end %>
  </div>
</div>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should now pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added project edit (view level)"
git push origin 05-06-project_edit
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
