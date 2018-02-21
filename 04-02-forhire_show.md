# Unit 4
## Chapter 2: Forhire Show Method
In this chapter, you will add the show method for the forhire object.  Please note that only the first forhire object of a given user is shown.  (As mentioned before, the number of forehire objects per user will be limited to one in a later chapter.)

### New Branch
Enter the command "git checkout -b 04-02-forhire_show".

### Part A: Controller Level

### Controller Testing
* Enter the command "rails generate controller Forhires".
* Enter the command "rm app/helpers/forhires_helper.rb".
* Edit the file test/controllers/forhires_controller_test.rb.  Replace everything between the line "class ForhiresControllerTest < ActionDispatch::IntegrationTest" and the last "end" command with the following:
```
  test 'forhire show action' do
    get forhire_path(@fh_connery)
    assert_response :success
    get forhire_path(@fh_lazenby)
    assert_response :success
    get forhire_path(@fh_moore)
    assert_response :success
    get forhire_path(@fh_dalton)
    assert_response :success
    get forhire_path(@fh_brosnan)
    assert_response :success
    get forhire_path(@fh_craig)
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  The new controller test fails because forhire_path is undefined.

#### Routing
* Edit the config/routes.rb file.  Just before the last "end" statement, add the following line:
```
  # BEGIN: forhire section
  resources :forhires, only: [:show]
  # END: forhire section
```
* Enter the command "sh testc.sh".  The new controller test fails because of missing routes.  (The forhire objects are not yet provided.)

#### Test Helper
* Edit the test/test_helper.rb file.  Just before the minitest-reporters section, add the following line:
```
require File.expand_path('../../test/setup_objects.rb', __FILE__)
```
* Edit the test/test_helper.rb file.  Just before the end of the definition of setup_universal function, add the following line:
```
  add_user_objects
```
* Add the file test/setup_objects.rb and give it the following code:
```
# rubocop:disable Metrics/MethodLength
# rubocop:disable Metrics/LineLength

def add_user_objects
  @fh_connery = @u1.forhires.create(description: 'I stopped Blofeld 4 times!',
                                    email: 'first_bond@rubyonracetracks.com',
                                    title: 'James Bond 1962-1971')
  @fh_lazenby = @u2.forhires.create(description: 'I was James Bond for one movie.',
                                    email: 'george_lazenby@rubyonracetracks.com',
                                    title: 'James Bond 1969')
  @fh_moore = @u3.forhires.create(description: 'I made James Bond campier.',
                                  email: 'roger_moore@rubyonracetracks.com',
                                  title: 'James Bond 1973-1985')
  @fh_dalton = @u4.forhires.create(description: 'I brought gritty realism to the Bond series.',
                                   email: 'timothy_dalton@rubyonracetracks.com',
                                   title: 'James Bond 1987-1989')
  @fh_brosnan = @u5.forhires.create(description: 'James Bond moved beyond the Cold War Era on my watch.',
                                    email: 'pierce_brosnan@rubyonracetracks.com',
                                    title: 'James Bond 1995-2002')
  @fh_craig = @u6.forhires.create(description: 'I rebooted James Bond.',
                                  email: 'daniel_craig@rubyonracetracks.com',
                                  title: 'James Bond 2006-')
end

# rubocop:enable Metrics/MethodLength
# rubocop:enable Metrics/LineLength
```
* Enter the command "sh testc.sh".  Now the test fails because the "show" action is not provided in the forhire controller.

#### Test Fixture Problems
* As you can see, this app does NOT use forhire test fixtures.  That's because my attempts to use forhire test fixtures did not pan out.
* The testing framework is set up to clean the testing environment's database between tests.  A forhire test fixture would have to belong to a user test fixture.  Clearing the user test fixture before clearing the associated forhire test fixture causes chaos in the PostgreSQL database.  The first time I ran tests after creating the forhire test fixtures, everything would work as expected.  The second time I ran tests, I was bombarded with PostgreSQL error messages about a referential integrity problem.
* One solution to the referential integrity problem was to execute the command "rails db:test:prepare" before each test.  Unfortunately, that took about 15 seconds, compared to less than 2 seconds to run the model tests and less than 4 seconds to run the controller tests.  Given how frequently I run tests, multiplying the amount of time needed was unacceptable.
* Another solution to the referential integrity problem was to use the DatabaseCleaner gem.  Unfortunately, that severely slowed down the tests.  For example, it took over a minute to run the controller tests.
* Some people use factories instead of test fixtures and RSpec instead of Minitest.  Unfortunately, that would require rewriting the Rails Neutrino script that builds the template app that I use as a basis for new Rails apps.
* Thus, my solution is the test/setup_objects.rb script.  It's unorthodox, but it gets the job done quickly without cutting corners on quality.

#### Controller
* Edit the file app/controllers/forhires_controller.rb.  Add the line "#" immediately before the line "class ForhiresController < ApplicationController".
* Edit the file app/controllers/forhires_controller.rb. Insert the following lines between "class ForhiresController < ApplicationController" and "end":
```
  # BEGIN: action section
  def show
    @forhire = Forhire.find(params[:id])
    @user = User.where("id=#{@forhire.user_id}").first
  end
  # END: action section
```
* Enter the command "sh testc.sh". The controller test fails because of a missing template.
* Enter the command "touch app/views/forhires/show.html.erb" to provide the template. (You'll add content to it later.)
* Enter the command "sh testc.sh". All tests should now pass.
* Enter the command "sh git_check.sh". All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the forhire show capability (controller level)"
```

### Part B: View Level

#### Integration Testing
* Enter the command "rails generate integration_test forhire_show".
* Edit the file test/integration/forhire_show_test.rb.  Replace everything between the line "class ForhireShowTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def check_forhire_pages
    visit forhire_path(@fh_connery)
    assert page.has_css?('title',
                         text: full_title('Sean Connery: James Bond 1962-1971'),
                         visible: false)
    assert page.has_css?('h1', text: 'For Hire')
    assert_text 'I stopped Blofeld 4 times!'
    assert_text 'first_bond@rubyonracetracks.com'
    assert_text 'James Bond 1962-1971'

    visit forhire_path(@fh_lazenby)
    assert page.has_css?('title',
                         text: full_title('George Lazenby: James Bond 1969'),
                         visible: false)
    assert page.has_css?('h1', text: 'For Hire')
    assert_text 'I was James Bond for one movie.'
    assert_text 'george_lazenby@rubyonracetracks.com'
    assert_text 'James Bond 1969'
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

  def check_forhire_for_link
    visit forhire_path(@fh_connery)
    assert page.has_link?('Sean Connery', href: user_path(@u1))
    visit forhire_path(@fh_lazenby)
    assert page.has_link?('George Lazenby', href: user_path(@u2))
  end

  test 'visitor sees the expected content on pages' do
    check_forhire_pages
    visit forhire_path(@fh_connery)
    assert_not page.has_link?('Sean Connery', href: user_path(@u1))
    visit forhire_path(@fh_lazenby)
    assert_not page.has_link?('George Lazenby', href: user_path(@u2))
  end

  test 'user sees the expected content on pages' do
    login_as(@u1, scope: :user)
    check_forhire_pages
    check_forhire_for_link
  end

  test 'regular admin sees the expected content on pages' do
    login_as(@a4, scope: :admin)
    check_forhire_pages
    check_forhire_for_link
  end

  test 'super admin sees the expected content on pages' do
    login_as(@a1, scope: :admin)
    check_forhire_pages
    check_forhire_for_link
  end
```
* Please note that unregistered visitors do not get a link to the user profile page, because they do not have access to it.
* Enter the command "sh test_app.sh".  All 4 new integration tests fail.
* Enter the command "alias test1='Command to run failed tests minus the TESTOPTS portion'".
* Enter the command "test1".  The same 4 integration tests fail because the forhire profile page does not have the expected content.

#### Forhire Profile Page
* Fill in the blank app/views/forhires/show.html.erb file with the following code:
```
<% provide(:title, "#{@user.first_name} #{@user.last_name}: #{@forhire.title}") %>

<h1>For Hire</h1>

<% # BEGIN: name and title of user %>
<% if admin_signed_in? || user_signed_in? %>
  <%= link_to "#{@user.first_name} #{@user.last_name}", @user %>, <%= @forhire.title %>
<% else %>
  <%= @user.first_name %> <%= @user.last_name %>, <%= @forhire.title %>
<% end %>
<% # END: name and title of user %>
<br><br>
Email: <%= raw(EmailMunger.encode(@forhire.email)) %>
<br><br>
<%= @forhire.description %>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the forhire show capability (view level)"
git push origin 04-02-forhire_show
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Your app will be flagged by Hakiri for cross-site scripting for the display of the email address in app/views/forhires/show.html.erb.  Mark this as a false positive.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
