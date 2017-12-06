# Unit 4
## Chapter 2: Forhire Show Method
In this chapter, you will add the show method for the forhire object.  Please note that only the first forhire object of a given is shown.  (As mentioned before, the number of forehire objects per user will be limited to one in a later chapter.)

### New Branch
Enter the command "git checkout -b 04-02-forhire_show".

### Part A: Controller Level

### Controller Test
* Enter the command "rails generate controller Forhires".
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
* Enter the command "sh testc.sh".  The new controller test fails because of missing routes.  (The forhire objects are not yet provided.)

#### Test Fixtures and Helper
* Replace the contents of the file test/fixtures/for_hires.yml with the following code:
```
bond_connery:
  blurb: 'I was the original James Bond.'
  email: 'sean_connery@rubyonracetracks.com'
  title: 'James Bond 1962-1971'
  user: lazenby
  created_at: <%= Time.new(1962, 10, 5) %>
  updated_at: <%= Time.new(1971, 12, 14) %>

bond_lazenby:
  blurb: 'I was James Bond for one movie.'
  email: 'george_lazenby@rubyonracetracks.com'
  title: 'James Bond 1969'
  user: lazenby
  created_at: <%= Time.new(1969, 12, 18, 0, 1) %>
  updated_at: <%= Time.new(1969, 12, 18, 23, 59) %>

bond_moore:
  blurb: 'I made James Bond campier.'
  email: 'roger_moore@rubyonracetracks.com'
  title: 'James Bond 1973-1985'
  user: moore
  created_at: <%= Time.new(1973, 6, 27) %>
  updated_at: <%= Time.new(1985, 5, 22) %>

bond_dalton:
  blurb: 'I brought gritty realism to the Bond series.'
  email: 'timothy_dalton@rubyonracetracks.com'
  title: 'James Bond 1987-1989'
  user: dalton
  created_at: <%= Time.new(1987, 6, 29) %>
  updated_at: <%= Time.new(1989, 6, 13) %>

bond_brosnan:
  blurb: 'James Bond moved beyond the Cold War Era on my watch.'
  email: 'pierce_brosnan@rubyonracetracks.com'
  title: 'James Bond 1995-2002'
  user: brosnan
  created_at: <%= Time.new(1995, 11, 13) %>
  updated_at: <%= Time.new(2002, 11, 20) %>

bond_craig:
  blurb: 'I rebooted James Bond.'
  email: 'daniel_craig@rubyonracetracks.com'
  title: 'James Bond 2006-'
  user: craig
  created_at: <%= Time.new(2006, 11, 14) %>
  updated_at: <%= Time.now %>
```
* Edit the file test/test_helper.rb.  Just before the end of the definition of setup_universal, add the following lines:
```

  @fh_connery = forhires(:bond_connery)
  @fh_lazenby = forhires(:bond_lazenby)
  @fh_moore = forhires(:bond_moore)
  @fh_dalton = forhires(:bond_dalton)
  @fh_brosnan = forhires(:bond_brosnan)
  @fh_craig = forhires(:bond_craig)
```
* Enter the command "sh testc.sh".  The controller test fails because the show action is missing.
* Enter the command "sh testc.sh".  You now get a long cascade of referential integrity errors.
* What's the problem?  Because each forhire object belongs to a user object, deleting the user object before deleting the associated forhire object causes chaos.  During the process of deleting objects between tests, this is basically what happens when you use PostgreSQL as your database in the test environment.
* Enter the command "sh testm.sh".  You get the same long cascade of referential integrity errors.
* Enter the command "sh test_app.sh".  The same long cascade of referential integrity errors appears again.
* Enter the command "sh testh.sh".  There's only one referential integrity error, but that's because there's only one helper test.
* Enter the command "rails db:test:prepare; sh testc.sh".  You now get the expected result.  The controller test fails because the show action is missing.
* Enter the command "rails db:test:prepare; sh testm.sh".  All model tests should pass.
* Enter the command "rails db:test:prepare; sh testm.sh".  Your helper test should pass.
* Enter the command "rails db:test:prepare; sh test_app.sh".  The controller test fails because the show action is missing.
* In other words, this app now requires you to execute the command "rails db:test:prepare" before running any "rails test" command in order to prevent the referential integrity error.  To execute this action on autopilot, testing scripts must be updated to include this corrective action.
* NOTE: I tried using the database_cleaner gem, but the truncation mode that worked severely slowed down the tests.  For example, the controller tests that should have taken only about 3 seconds to execute instead took over a minute.

#### Updating the Bash Scripts For Testing
* Edit the file testc.sh.  Immediately after the comments, insert the following code:
```
echo '---------------------'
echo 'rails db:test:prepare'
rails db:test:prepare
```
* Enter the command "sh testc.sh".  There should be just one error.
* Edit the file testm.sh.  Immediately after the comments, insert the following code:
```
echo '---------------------'
echo 'rails db:test:prepare'
rails db:test:prepare
```
* Enter the command "sh testm.sh".  All tests should pass.
* Edit the file testh.sh.  Immediately after the comments, insert the following code:
```
echo '---------------------'
echo 'rails db:test:prepare'
rails db:test:prepare
```
* Enter the command "sh testh.sh".  The test should pass.
* Enter the command "sh test_app.sh".  Just before the "rails test" section, insert the following code:
```
echo '---------------------'
echo 'rails db:test:prepare'
rails db:test:prepare
```
* Edit the file test/test_helper.rb.  In the line that begins with "reporter_options", change the value of rerun_prefix to the following:
```
rm -f log/test.log; rails db:test:prepare; bundle exec
```

#### Controller
* Edit the file app/controllers/forhires_controller.rb. Just before the line "class ForhiresController < ApplicationController", add the line "#".
* Edit the file app/controllers/forhires_controller.rb. Insert the following lines between "class ForhiresController < ApplicationController" and "end":
```
  # BEGIN: action section
  def show
    @forhire = Forhire.find(params[:id])
  end
  # END: action section
```
* Enter the command "sh testc.sh". The controller test fails because of a missing template.
* Enter the command "touch app/views/forhires/show.html.erb" to provide the template. (You'll add content to it later.)
* Enter the command "sh testc.sh". All tests should now pass.
* Enter the command "rm app/helpers/forhires_helper.rb".
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
