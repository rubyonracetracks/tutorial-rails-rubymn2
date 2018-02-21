# Unit 5
## Chapter 3: Project Index

In this chapter, you will add the project index.

### New Branch
Enter the command "git checkout -b 05-03-project_index".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/projects_controller_test.rb.  Add the following lines before the last "end" statement:
```
  test 'project index action' do
    get projects_path
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  Your new controller test fails because projects_path is undefined.

#### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :projects" with the following:
```
  resources :projects, only: [:show, :index]
```
* Enter the command "sh testc.sh".  The test fails because the index action is not defined in the project controller.

#### Controller
* Edit the file app/controllers/projects_controller.rb.  Just before the end of the action section, add the following lines:
```
  def index
    @projects = Project.order('updated_at DESC').page(params[:page]).per(50)
    @projects_count = Project.count
  end
```
* Enter the command "sh testc.sh".  The test fails because of a missing template.

#### Blank View
* Enter the command "touch app/views/projects/index.html.erb".  Leave this file blank for now.  (You'll fill it in soon.)
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the project index capability (controller level)"
```

### Part B: View Level

#### Setup Objects Script
* Edit the file test/setup_objects.rb.  Just after the last "end" statement, add the following code:
```
# rubocop:disable Metrics/AbcSize
# rubocop:disable Metrics/MethodLength
# rubocop:disable Metrics/LineLength
def add_extra_projects
  users = User.all
  n = 0
  users.each do |u|
    n += 1
    next if n < 20 || rand < 0.2 || u.projects.count > 0
    u.projects.create(title: "Project #{n}: #{Faker::Company.catch_phrase}",
                      source_code_url: Faker::Internet.url,
                      deployed_url: Faker::Internet.url,
                      description: "Description #{n}: #{Faker::Lorem.paragraph(10)}",
                      created_at: 11.minutes.ago,
                      updated_at: 10.minutes.ago)
  end
end
# rubocop:enable Metrics/AbcSize
# rubocop:enable Metrics/MethodLength
# rubocop:enable Metrics/LineLength
```
* Please note that the add_extra_projects function is used only in the tests that require it.  Adding the extra project objects in every test would excessively slow down the testing process.

#### Integration Test
* Enter the command "rails generate integration_test project_index".
* Edit the file test/integration/project_index_test.rb.  Replace everything between "class ProjectIndexTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  # rubocop:disable Metrics/BlockLength
  test 'The project index page has the expected content' do
    add_extra_projects
    visit projects_path
    assert_text 'Roger Moore'
    assert_text 'Live and Let Die'
    assert_text 'The Man with the Golden Gun'
    assert_text 'Pierce Brosnan'
    assert_text 'GoldenEye'
    assert_text 'Tomorrow Never Dies'
    assert_text 'Daniel Craig'
    assert_text 'Quantum of Solace'

    # Verify that index page provides access to profile pages
    assert page.has_css?('title', text: full_title('Project Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Project Index')
    assert page.has_link?('Live and Let Die', href: project_path(@p1))
    assert page.has_link?('The Man with the Golden Gun', href: project_path(@p2))
    assert page.has_link?('GoldenEye', href: project_path(@p3))
    assert page.has_link?('Tomorrow Never Dies', href: project_path(@p4))
    assert page.has_link?('Quantum of Solace', href: project_path(@p5))

    # Verify that the index page provides access to the source code and
    # deployed sites
    assert page.has_link?('https://github.com/rmoore/kananga',
                          href: 'https://github.com/rmoore/kananga')
    assert page.has_link?('http://www.kananga.com',
                          href: 'http://www.kananga.com')
    assert page.has_link?('https://github.com/rmoore/scaramanga',
                          href: 'https://github.com/rmoore/scaramanga')
    assert page.has_link?('http://www.scaramanga.com',
                          href: 'http://www.scaramanga.com')
    assert page.has_link?('ttps://github.com/pbrosnan/goldeneye',
                          href: 'https://github.com/pbrosnan/goldeneye')
    assert page.has_link?('http://www.goldeneye.com',
                          href: 'http://www.goldeneye.com')
    assert page.has_link?('https://github.com/pbrosnan/carver',
                          href: 'https://github.com/pbrosnan/carver')
    assert page.has_link?('http://www.carvernewsnetwork.com',
                          href: 'http://www.carvernewsnetwork.com')

    # Verify that root page provides access to index page
    click_on 'Home'
    assert page.has_link?('Projects', href: projects_path)

    # Verify that the second page of the index works
    click_on 'Projects'
    first(:link, '2').click
    assert page.has_css?('title', text: full_title('Project Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Project Index')
  end
  # rubocop:enable Metrics/BlockLength
```
* Enter the command "sh test_app.sh".  Your new integration test fails.
* Enter the command "alias test1='(command to run the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same integration test fails because the project index page does not have the expected content.

#### Project Index Page
* Fill in the blank app/views/projects/index.html.erb file with the following code:
```
<% provide(:title, 'Project Index') %>

<h1>Project Index</h1>
Number of projects: <%= @projects_count %>
<br>
<%= paginate @projects %>

<%= render @projects %>
<hr>

<%= paginate @projects %>
```
* Create the file app/views/projects/_project.html.erb with the following content:
```
<hr>
<h3><%= @project.title %></h3>
<% user_f = User.where(id: "#{@project.user_id}") %>
<%= user_f.first_name %> <%= user_f.last_name %>
<br>
<% if @project.source_code_url.nil? == false %>
  Source Code URL: <%= link_to "#{@project.source_code_url}", @project.source_code_url %>
  <br>
<% end %>
<% if @project.deployed_url.nil? == false %>
  Deployed URL: <%= link_to "#{@project.deployed_url}", @project.deployed_url %>
  <br>
<% end %>
<%= @project.description[0..140] %>
```
* Enter the command "test1".  The same integration test fails for a different reason.  The project index page has the expected content, but the link to this page from the home page has not yet been added.

#### Header
* Edit the file app/views/layouts/_header.html.erb.  Just before the beginning of the variable section, add the following line:
```
        <li><%= link_to "Projects", projects_path %></li>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Take a look at the project index page of your app app in the local browser.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the project index capability (view level)"
git push origin 05-03-project_index
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
