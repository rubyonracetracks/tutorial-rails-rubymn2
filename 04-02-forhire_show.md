# Unit 4
## Chapter 2: Forhire Show Method
In this chapter, you will add the show method for the forhire object.  Please note that only the first forhire object of a given user is shown.  (As mentioned before, the number of forehire objects per user will be limited to one in a later chapter.)

### New Branch
Enter the command "git checkout -b 04-02-forhire_show".

### Part A: Controller Level

### Controller Test
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
  resources :forhires, only: [:show]
```
* Add the following line just before the line "Rails.application.routes.draw do"
```
# rubocop:disable Metrics/BlockLength
```
* Add the following line just after the last end statement:
```
# rubocop:enable Metrics/BlockLength
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
def add_user_objects
  add_forhires
end

def add_forhires
  @fh_connery = @u1.forhires.create(blurb: 'I stopped Blofeld 4 times!',
                                    email: 'first_bond@rubyonracetracks.com',
                                    title: 'James Bond 1962-1971')
  @fh_lazenby = @u2.forhires.create(blurb: 'I was James Bond for one movie.',
                                    email: 'george_lazenby@rubyonracetracks.com',
                                    title: 'James Bond 1969')
  @fh_moore = @u3.forhires.create(blurb: 'I made James Bond campier.',
                                  email: 'roger_moore@rubyonracetracks.com',
                                  title: 'James Bond 1973-1985')
  @fh_dalton = @u4.forhires.create(blurb: 'I brought gritty realism to the Bond series.',
                                   email: 'timothy_dalton@rubyonracetracks.com',
                                   title: 'James Bond 1987-1989')
  @fh_brosnan = @u5.forhires.create(blurb: 'James Bond moved beyond the Cold War Era on my watch.',
                                    email: 'pierce_brosnan@rubyonracetracks.com',
                                    title: 'James Bond 1995-2002')
  @fh_craig = @u6.forhires.create(blurb: 'I rebooted James Bond.',
                                  email: 'daniel_craig@rubyonracetracks.com',
                                  title: 'James Bond 2006-')
end
```

#### Test Fixture Problems
* As you can see, this app does NOT use forhire test fixtures.  That's because my attempts to use forhire test fixtures did not pan out.
* The testing framework is set up to clean the testing environment's database between tests.  A forhire test fixture would have to belong to a user test fixture.  Clearing the user test fixture before clearing the associated forhire test fixture causes chaos in the PostgreSQL database.  The first time I ran tests after creating the forhire test fixtures, everything would work as expected.  The second time I ran tests, I was bombarded with PostgreSQL error messages about a referential integrity problem.
* One solution to the referential integrity problem was to execute the command "rails db:test:prepare" before each test.  Unfortunately, that took about 15 seconds, compared to less than 2 seconds to run the model tests and less than 4 seconds to run the controller tests.  Given how frequently I run tests, multiplying the amount of time needed was unacceptable.
* Another solution to the referential integrity problem was to use the DatabaseCleaner gem.  Unfortunately, that severely slowed down the tests.  For example, it took over a minute to run the controller tests.
* Some people use factories instead of test fixtures and RSpec instead of Minitest.  Unfortunately, that would require rewriting the Rails Neutrino script that builds the template app that I use as a basis for new Rails apps.
* Thus, my solution is the scripts in test/setup_objects.rb.  It's unorthodox, but it gets the job done quickly without cutting corners on quality.

#### Controller
* Edit the file app/controllers/forhires_controller.rb.  Add the line "#" immediately before the line "class ForhiresController < ApplicationController".
* Edit the file app/controllers/forhires_controller.rb. Insert the following lines between "class ForhiresController < ApplicationController" and "end":
```
  # BEGIN: action section
  def show
    @forhire = Forhire.find(params[:id])
    @user = User.where("id=#{@forhire.user_id}")
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
* Enter the command "rails generate integration_test forhire_show".
* Edit the file test/integration/forhire_show_test.rb.  Just before the line "class ForhireShowTest < ActionDispatch::IntegrationTest", add the line "# rubocop:disable Metrics/BlockLength".
* Edit the file test/integration/forhire_show_test.rb.  After the last "end" statement, add the line "# rubocop:enable Metrics/BlockLength".
* Edit the file test/integration/forhire_show_test.rb.  Replace everything between the line "class ForhireShowTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
```

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the forhire show capability"
git push origin 04-02-forhire_show
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
