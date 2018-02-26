# Unit 6
## Chapter 3: Opening Index

In this chapter, you will add the opening index.

### New Branch
Enter the command "git checkout -b 06-03-opening_index".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/openings_controller_test.rb.
  Add the following lines before the last "end" statement:
```
  test 'unregistered visitor redirected from opening index page' do
    get openings_path
    assert_redirected_to new_user_session_path
  end

  test 'user can access opening index page' do
    sign_in @u1, scope: :user
    get openings_path
    assert_response :success
  end

  test 'regular admin can access opening index page' do
    sign_in @a4, scope: :admin
    get openings_path
    assert_response :success
  end

  test 'super admin can access opening index page' do
    sign_in @a1, scope: :admin
    get openings_path
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  Your 4 new controller tests fail because openings_path is undefined.

#### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :openings" with the following:
```
  resources :openings, only: [:show, :index]
```
* Enter the command "sh testc.sh".  The 4 controller tests fail because the index action is not defined in the opening controller.

#### Controller
* Edit the file app/controllers/openings_controller.rb.  Replace the line containing "before_action :may_view_project" with the following:
```
  before_action :may_view_project, only: [:show, :index]
```
* Edit the file app/controllers/openings_controller.rb.  Just before the end of the action section, add the following lines:
```
  def index
    @openings = Opening.order('updated_at DESC').page(params[:page]).per(50)
    @openings_count = Opening.count
  end
```
* Enter the command "sh testc.sh".  3 tests still fail because of a missing template.

#### Blank View
* Enter the command "touch app/views/openings/index.html.erb".  Leave this file blank for now.  (You'll fill it in soon.)
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the opening index capability (controller level)"
```

### Part B: View Level

#### Setup Objects Script
* Edit the file test/setup_objects.rb.  Just after the last "end" statement, add the following code:
```
# rubocop:disable Metrics/AbcSize
# rubocop:disable Metrics/MethodLength
# rubocop:disable Metrics/LineLength
def add_extra_openings
  users = User.all
  n = 0
  users.each do |u|
    n += 1
    next if n < 20 || rand < 0.2 || u.openings.count > 0
    u.openings.create(title: "Opening #{n}: #{Faker::Job.title}",
                      description: "Description #{n}: #{Faker::Lorem.paragraph(10)}",
                      created_at: 11.minutes.ago,
                      updated_at: 10.minutes.ago)
  end
end
# rubocop:enable Metrics/AbcSize
# rubocop:enable Metrics/MethodLength
# rubocop:enable Metrics/LineLength
```
* Please note that the add_extra_openings function is used only in the tests that require it.  Adding the extra opening objects in every test would excessively slow down the testing process.

#### Integration Test
* Enter the command "rails generate integration_test opening_index".
* Edit the file test/integration/opening_index_test.rb.  Replace everything between "class OpeningIndexTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  # rubocop:disable Metrics/BlockLength
  def opening_index_enabled
    add_extra_openings
    visit openings_path
    assert_text 'Hijack American and Soviet spacecraft'
    assert_text 'Run a European crime syndicate'
    assert_text 'Create fake versions of Blofeld to foil 007'
    assert_text 'Catch that black Trans Am!'
    assert_text 'Fix those police cars I keep wrecking'

    # Verify that index page provides access to user profile pages
    assert page.has_link?('Ernst Blofeld', href: user_path(@u7))
    assert page.has_link?('Jackie Gleason', href: user_path(@u11))

    # Verify that index page provides access to opening profile pages
    assert page.has_css?('title', text: full_title('Opening Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Opening Index')
    assert page.has_link?('Spacecraft Hijacker', href: opening_path(@op1))
    assert page.has_link?('Head of Unione Corse', href: opening_path(@op2))
    assert page.has_link?('Plastic Surgeon', href: opening_path(@op3))
    assert page.has_link?('Deputy', href: opening_path(@op4))
    assert page.has_link?('Body Repair Technician', href: opening_path(@op5))

    # Verify that root page provides access to index page
    click_on 'Home'
    assert page.has_link?('openings', href: openings_path)

    # Verify that the second page of the index works
    click_on 'Openings'
    first(:link, '2').click
    assert page.has_css?('title', text: full_title('Job Opening Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Jop Opening Index')
  end
  # rubocop:enable Metrics/BlockLength

  test 'unregistered visitor redirected to user login page' do
    get openings_path
    assert_redirected_to new_user_session_path
  end

  test 'user can view the opening index page' do
    login_as(@u1, scope: :user)
    opening_index_enabled
  end

  test 'regular admin can view the opening index page' do
    login_as(@a4, scope: :admin)
    opening_index_enabled
  end

  test 'super admin can view the opening index page' do
    login_as(@a1, scope: :admin)
    opening_index_enabled
  end
```
* Enter the command "sh test_app.sh".  Your new integration test fails.
* Enter the command "alias test1='(command to run the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same integration test fails because the opening index page does not have the expected content.

#### Job Opening Index Page
* Fill in the blank app/views/projects/index.html.erb file with the following code:
```
<% provide(:title, 'Job Opening Index') %>

<h1>Job Opening Index</h1>
Number of openings: <%= @openings_count %>
<br>
<%= paginate @openings %>

<%= render @openings %>
<hr>

<%= paginate @openings %>
```
* Create the file app/views/openings/_opening.html.erb with the following content:
```
<hr>
<h4><%= link_to "#{opening.title}", opening_path(opening) %></h4>
<% user_f = User.where(id: "#{opening.user_id}").first %>
Posted by: <%= user_f.first_name %> <%= user_f.last_name %>
<br>
<%= opening.description[0..140] %>
```
* Enter the command "test1".  The same integration test fails for a different reason.  The opening index page has the expected content, but the link to this page from the home page has not yet been added.

#### Header
* Edit the file app/views/layouts/_header.html.erb.  Just before the beginning of the variable section, add the following line:
```
        <li><%= link_to "Job Openings", openings_path %></li>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Take a look at the opening index page of your app app in the local browser.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the opening index capability (view level)"
git push origin 06-03-opening_index
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
