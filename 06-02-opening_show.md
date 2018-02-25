# Unit 6
## Chapter 2: Opening Show Method
In this chapter, you will add the show method for the opening object.  Note that unregistered visitors are directed to login first.

### New Branch
Enter the command "git checkout -b 06-02-opening_show".

### Part A: Controller Level

### Controller Testing
* Enter the command "rails generate controller Openings".
* Enter the command "rm app/helpers/openings_helper.rb".
* Edit the file test/controllers/openings_controller_test.rb.  Replace everything between the line "class OpeningsControllerTest < ActionDispatch::IntegrationTest" and the last "end" command with the following:
```
  # BEGIN: definitions
  def opening_show_enabled
    get opening_path(@op1)
    assert_response :success
    get opening_path(@op2)
    assert_response :success
    get opening_path(@op3)
    assert_response :success
    get opening_path(@op4)
    assert_response :success
    get opening_path(@op5)
    assert_response :success
  end
  # END: definitions

  test 'unregistered visitor redirected to user login page' do
    get opening_path(@op1)
    assert_redirected_to new_user_session_path
    get opening_path(@op2)
    assert_redirected_to new_user_session_path
    get opening_path(@op3)
    assert_redirected_to new_user_session_path
    get opening_path(@op4)
    assert_redirected_to new_user_session_path
    get opening_path(@op5)
    assert_redirected_to new_user_session_path
  end

  test 'user can access job openings' do
    sign_in @u7, scope: :user
    opening_show_enabled
  end

  test 'regular admin can access job openings' do
    sign_in @a4, scope: :admin
    opening_show_enabled
  end

  test 'super admin can access job openings' do
    sign_in @a1, scope: :admin
    opening_show_enabled
  end
```
* Enter the command "sh testc.sh".  All 4 new controller tests fail because opening_path is undefined.

#### Routing
* Edit the config/routes.rb file.  Just before the last "end" statement, add the following line:
```
  # BEGIN: opening section
  resources :openings, only: [:show]
  # END: opening section
```
* Enter the command "sh testc.sh".  The tests fail because of missing routes.  (The opening objects are not yet provided.)

#### Test Helper
* In the file test/setup_objects.rb, add the following lines just before the end of the definition of add_user_objects:
```
  @op1 = @u7.openings.create(title: 'Spacecraft Hijacker',
                             description: 'Hijack American and Soviet spacecraft')
  @op2 = @u7.openings.create(title: 'Head of Unione Corse',
                             description: 'Run a European crime syndicate')
  @op3 = @u7.openings.create(title: 'Plastic Surgeon',
                             description: 'Create fake versions of Blofeld to foil 007')
  @op4 = @u11.openings.create(title: 'Deputy',
                              description: 'Catch that black Trans Am!')
  @op5 = @u11.openings.create(title: 'Body Repair Technician',
                              description: 'Fix those police cars I keep wrecking')
```
* Enter the command "sh testc.sh".  Now the tests fail because the "show" action is not provided in the opening controller.

#### Controller
* Edit the file app/controllers/openings_controller.rb.  Add the line "#" immediately before the line "class OpeningsController < ApplicationController".
* Edit the file app/controllers/openings_controller.rb. Insert the following lines between "class OpeningsController < ApplicationController" and "end":
```
  # BEGIN: before_action section
  before_action :may_view_project, only: [:show]
  # END: before_action section

  # BEGIN: action section
  def show
    @opening = Opening.find(params[:id])
    @user = User.where("id=#{@opening.user_id}").first
  end
  # END: action section

  private

  # BEGIN: private section
  def may_view_project
    return if user_signed_in? || admin_signed_in?
    flash[:alert] = 'You must be logged in to view job openings.'
    redirect_to(new_user_session_path)
  end
  helper_method :may_view_project
  # END: private section
```
* Enter the command "sh testc.sh". 3 tests fail because of a missing template.
* Enter the command "touch app/views/openings/show.html.erb" to provide the template. (You'll add content to it later.)
* Enter the command "sh testc.sh". All tests should now pass.
* Enter the command "sh git_check.sh". All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the opening show capability (controller level)"
```

### Part B: View Level

#### Integration Testing
* Enter the command "rails generate integration_test opening_show".
* Edit the file test/integration/opening_show_test.rb.  Replace everything between the line "class OpeningShowTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def check_opening_pages
    visit opening_path(@op1)
    assert page.has_css?('title',
                         text: full_title('Job Opening: Spacecraft Hijacker'),
                         visible: false)
    assert page.has_css?('h1', text: 'Job Opening: Spacecraft Hijacker')
    assert_text 'Hijack American and Soviet spacecraft'
    assert page.has_link?('Ernst Blofeld', href: user_path(@u7))

    visit opening_path(@op2)
    assert page.has_css?('title',
                         text: full_title('Job Opening: Head of Unione Corse'),
                         visible: false)
    assert page.has_css?('h1', text: 'Job Opening: Head of Unione Corse')
    assert_text 'Run a European crime syndicate'
    assert page.has_link?('Ernst Blofeld', href: user_path(@u7))

    visit opening_path(@op3)
    assert page.has_css?('title',
                         text: full_title('Job Opening: Plastic Surgeon'),
                         visible: false)
    assert page.has_css?('h1', text: 'Job Opening: Plastic Surgeon')
    assert_text 'Create fake versions of Blofeld to foil 007'
    assert page.has_link?('Ernst Blofeld', href: user_path(@u7))

    visit opening_path(@op4)
    assert page.has_css?('title',
                         text: full_title('Job Opening: Deputy'),
                         visible: false)
    assert page.has_css?('h1', text: 'Job Opening: Deputy')
    assert_text 'Catch that black Trans Am!'
    assert page.has_link?('Jackie Gleason', href: user_path(@u11))

    visit opening_path(@op5)
    assert page.has_css?('title',
                         text: full_title('Job Opening: Body Repair Technician'),
                         visible: false)
    assert page.has_css?('h1', text: 'Job Opening: Body Repair Technician')
    assert_text 'Fix those police cars I keep wrecking'
    assert page.has_link?('Jackie Gleason', href: user_path(@u11))
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

  def check_redirect(proj)
    visit opening_path(proj)
    assert_text 'You must be logged in to view job openings.'
    assert page.has_css?('title', text: full_title('User Login'), visible: false)
    assert page.has_css?('h1', text: 'User Login')
  end

  test 'unregistered visitor is redirected to the user login page' do
    check_redirect(@op1)
    check_redirect(@op2)
    check_redirect(@op3)
    check_redirect(@op4)
    check_redirect(@op5)
  end

  test 'user sees the expected content on pages' do
    login_as(@u1, scope: :user)
    check_opening_pages
  end

  test 'regular admin sees the expected content on pages' do
    login_as(@a4, scope: :admin)
    check_opening_pages
  end

  test 'super admin sees the expected content on pages' do
    login_as(@a1, scope: :admin)
    check_opening_pages
  end
```
* Enter the command "sh test_app.sh".  All 3 new integration tests fail.
* Enter the command "alias test1='Command to run failed tests minus the TESTOPTS portion'".
* Enter the command "test1".  The same 3 integration tests fail because the opening profile page does not have the expected content.

#### Opening Profile Page
* Fill in the blank app/views/openings/show.html.erb file with the following code:
```
<% provide(:title, "Job Opening: #{@opening.title}") %>

<h1>Job Opening: <%= @opening.title %></h1>

Posted by: <%= link_to "#{@user.first_name} #{@user.last_name}", @user %>
<br><br>
Description: <%= @opening.description %>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the opening show capability (view level)"
git push origin 06-02-opening_show
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
