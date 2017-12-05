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
* Add the following lines to the end of the file test/fixtures/for_hires.yml:
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
* Enter the command "sh testc.sh".  

#### Database Cleaner
* Add the following lines to the end of the Gemfile:
```
group :test do
  gem 'database_cleaner'
end
```
* NOTE: The changes in test/test_helper.rb are from https://github.com/appelier/bigtuna/blob/master/test/test_helper.rb .
* Edit the test/test_helper.rb file.  Add the following line just after the "require" statements at the beginning:
```
DatabaseCleaner.strategy = :truncation
```
* In the test/test_helper.rb file, add the following line to the beginning of the definition of setup (within class ActionDispatch::IntegrationTest):
```
    DatabaseCleaner.start
```
* In the test/test_helper.rb file, add the following line to the beginning of the definition of teardown (within class ActionDispatch::IntegrationTest):
```
    DatabaseCleaner.clean
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
