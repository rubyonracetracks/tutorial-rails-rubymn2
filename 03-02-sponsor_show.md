# Unit 3
## Chapter 2: Sponsor Show Method

In this chapter, you will add the show method for the sponsor object.

### New Branch
Enter the command "git checkout -b 03-02-sponsor_show".

### Part A: Controller Level

#### Controller Test
* Enter the command "rails generate controller Sponsors".
* Edit the file test/controllers/sponsors_controller_test.rb.  Replace everything between the line "class SponsorsControllerTest < ActionDispatch::IntegrationTest" and the last "end" command with the following:
```
  test 'sponsor show action' do
    get sponsor_path(@sponsor1)
    assert_response :success
    get sponsor_path(@sponsor2)
    assert_response :success
    get sponsor_path(@sponsor3)
    assert_response :success
    get sponsor_path(@sponsor4)
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  The new controller test fails because sponsor_path is undefined.

#### Routing
* Edit the config/routes.rb file.  Just before the last "end" statement, add the following line:
```
  resources :sponsors, only: [:show]
```
* Enter the command "sh testc.sh".  The new controller test fails because of missing routes.  (The sponsor objects are not yet provided.)

#### Test Fixtures and Helper
* In the file test/fixtures/sponsors.yml, replace the original sponsor test fixtures with the following:
```
blessed_buy:
  name: 'Blessed Buy'
  phone: '(612) 555-0101'
  description: 'THE place to buy the new Galaxy Wars merchandise'
  contact_email: 'example@blessedbuy.com'
  contact_url: 'http://www.blessedbuy.com'
  current: true

sky_store:
  name: 'Sky Store'
  phone: '(800) 555-0102'
  description: 'If you fly, you can buy overpriced junk from our catalog.'
  contact_email: 'example@skystore.com'
  contact_url: 'http://www.skystore.com'
  current: true

island_hoppers:
  name: 'Island Hoppers'
  phone: '(808) 555-0103'
  description: 'I provide helicopter rides around the Hawaiian Islands.'
  contact_email: 'tc@islandhoppers.com'
  contact_url: 'http://www.islandhoppers.com'
  current: false

flag:
  name: 'Foundation for Law and Government'
  phone: '(800) 555-0104'
  description: 'We built Kitt.  Michael Knight and Kitt solve crimes.'
  contact_email: 'devon_miles@flag.org'
  contact_url: 'http://www.flag.org'
  current: false
```
* Edit the file test/test_helper.rb.  Just before the end of the definition of setup_universal, add the following lines:
```

  @sponsor1 = sponsors(:blessed_buy)
  @sponsor2 = sponsors(:sky_store)
  @sponsor3 = sponsors(:island_hoppers)
  @sponsor4 = sponsors(:flag)
```
* Enter the command "sh testc.sh".  The controller test fails because the "show" action is not in the sponsor controller yet.

#### Controller and Template
* Edit the file app/controllers/sponsors_controller.rb.  Insert the following lines between "class SponsorsController < ApplicationController" and "end":
```
  # BEGIN: action section
  def show
    @sponsor = Sponsor.find(params[:id])
  end
  # END: action section
```
* Enter the command "sh testc.sh".  The controller test fails because of a missing template.
* Enter the command "touch app/views/sponsors/show.html.erb" to provide the template.  (You'll add content to it later.)
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and but there will be Rubocop offenses.
* Enter the command "rm app/helpers/sponsors_helper.rb".
* Edit the file app/controllers/sponsors_controller.rb.  Just before the line "class SponsorsController < ApplicationController", add the line "#".
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor show capability (controller level)"
```

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test sponsor_show".
* Edit the file test/integration/sponsor_show_test.rb.  Just before the line "class SponsorShowTest < ActionDispatch::IntegrationTest", add the line "# rubocop:disable Metrics/BlockLength".
* Edit the file test/integration/sponsor_show_test.rb.  After the last "end" statement, add the line "# rubocop:enable Metrics/BlockLength".
* Edit the file test/integration/sponsor_show_test.rb.  Replace everything between the line "class SponsorShowTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  test 'displays the sponsor profile pages' do
    visit sponsor_path(@sponsor1)
    assert page.has_css?('title', text: full_title('Blessed Buy'), visible: false)
    assert page.has_css?('h1', text: 'Current Sponsor: Blessed Buy')
    assert_text '(612) 555-0101'
    assert_text 'THE place to buy the new Galaxy Wars merchandise'
    assert_text 'example@blessedbuy.com'
    assert page.has_link?('http://www.blessedbuy.com', href: 'http://www.blessedbuy.com')

    visit sponsor_path(@sponsor2)
    assert page.has_css?('title', text: full_title('Sky Store'), visible: false)
    assert page.has_css?('h1', text: 'Current Sponsor: Sky Store')
    assert_text '(800) 555-0102'
    assert_text 'If you fly, you can buy overpriced junk from our catalog.'
    assert_text 'example@skystore.com'
    assert page.has_link?('http://www.skystore.com', href: 'http://www.skystore.com')

    visit sponsor_path(@sponsor3)
    assert page.has_css?('title', text: full_title('Island Hoppers'), visible: false)
    assert page.has_css?('h1', text: 'Past Sponsor: Island Hoppers')
    assert_text '(808) 555-0103'
    assert_text 'I provide helicopter rides around the Hawaiian Islands.'
    assert_text 'tc@islandhoppers.com'
    assert page.has_link?('http://www.islandhoppers.com', href: 'http://www.islandhoppers.com')

    visit sponsor_path(@sponsor4)
    assert page.has_css?('title', text: full_title('Foundation for Law and Government'), visible: false)
    assert page.has_css?('h1', text: 'Past Sponsor: Foundation for Law and Government')
    assert_text '(800) 555-0104'
    assert_text 'We built Kitt.  Michael Knight and Kitt solve crimes.'
    assert_text 'devon_miles@flag.org'
    assert page.has_link?('http://www.flag.org', href: 'http://www.flag.org')
  end
```
* Enter the command "sh test_app.sh".  One test fails.
* Enter the command "alias test1='(Command to run failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same test fails because the expected content is not present.

#### Updating the View
* Edit the blank app/views/sponsors/show.html.erb file.  Give it the following content:
```
<% provide(:title, @sponsor.name) %>

<% # BEGIN: h1 header section %>
<% if @sponsor.current == true %>
  <h1>Current Sponsor: <%= @sponsor.name %></h1>
<% else %>
  <h1>Past Sponsor: <%= @sponsor.name %></h1>
<% end %>
<% # END: h1 header section %>

Email: <%= raw(EmailMunger.encode(@sponsor.contact_email)) %>
<br>
Phone: <%= @sponsor.phone %>
<br>
URL: <%= link_to @sponsor.contact_url, @sponsor.contact_url %>
<br><br>
<%= @sponsor.description %>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor show capability"
git push origin 03-02-sponsor_show
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
