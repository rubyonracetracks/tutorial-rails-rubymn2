# Unit 3
## Chapter 3: Sponsor Index Controller

In this chapter, you will add the sponsor index.

### New Branch
Enter the command "git checkout -b 03-03-sponsor_index".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/sponsors_controller_test.rb.  Add the following lines before the last "end" statement:
```
  test 'sponsor index action' do
    get sponsors_path
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  Your new sponsor controller test fails because sponsors_path is undefined.

#### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :sponsors" with the following:
```
  resources :sponsors, only: [:show, :index]
```
* Enter the command "sh testc.sh".  The same test now fails because the index action is not provided by the sponsor controller.

#### Controller
* Edit the file app/controllers/sponsors_controller.rb.  Just before the end of the action section, add the following lines:
```
  def index
    ord = 'updated_at desc'
    @sponsors_current = Sponsor.where('current=?', true).order(ord)
    @sponsors_past = Sponsor.where('current!=?', true).order(ord)
  end
```
* Enter the command "sh testc.sh".  The test fails because of a missing template.

#### Blank View
* Enter the command "touch app/views/sponsors/index.html.erb".
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor index capability (controller level)"
```

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test sponsor_index".
* Edit the file test/integration/sponsor_index_test.rb.  Replace everything between "class SponsorIndexTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  test 'The sponsor index page has the expected content' do
    visit sponsors_path
    assert page.has_css?('title', text: full_title('Sponsor Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Sponsor Index')
    assert page.has_link?('Blessed Buy', href: sponsor_path(@sponsor1))
    assert page.has_link?('Sky Store', href: sponsor_path(@sponsor2))
    assert page.has_link?('Island Hoppers', href: sponsor_path(@sponsor3))
    assert page.has_link?('Foundation for Law and Government', href: sponsor_path(@sponsor4))
  end

  test 'The header provides access to the sponsor index page' do
    visit root_path
    assert page.has_link?('Sponsors', href: sponsors_path)
  end

  test 'The home page lists the current sponsors' do
    visit root_path
    assert page.has_link?('Blessed Buy', href: sponsor_path(@sponsor1))
    assert page.has_link?('Sky Store', href: sponsor_path(@sponsor2))
  end
```
* Enter the command "sh test_app.sh".  Your 3 new integration tests fail.
* Enter the command "alias test1='(command to run the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same 3 integration tests fail again.

#### Index Page
* Edit the blank file app/views/sponsors/index.html.erb.  Give it the following content:
```
<% provide(:title, 'Sponsor Index') %>

<h1>Sponsor Index</h1>

<h3>Current Sponsors</h3>
<div class="sponsors_current">
  <% @sponsors_current.each do |s| %>
    <li>
      <%= link_to s.name, s %>
    </li>
  <% end %>
</div>


<h3>Past Sponsors</h3>
<div class="sponsors_past">
  <% @sponsors_past.each do |s| %>
    <li>
      <%= link_to s.name, s %>
    </li>
  <% end %>
</div>
```
* NOTE: The sponsors_current and sponsors_past classes are not relevant yet but will be relevant when company logos are added.
* Enter the command "test1".  One of the new integration tests now passes because the sponsor index page has the expected content.  The other two tests still fail.

#### Header
* Edit the file app/views/layouts/_header.html.erb. Just before the beginning of the variable section, add the following line:
```
        <li><%= link_to "Sponsors", sponsors_path %></li>
```
* Enter the command "test1".  Now just one test still fails.

#### Home Page
* Displaying the current sponsors on the home page requires updating the home action in the static pages controller to acquire the list of current sponsors.
* Edit the file app/controllers/static_pages_controller.rb.  Replace the definition of home with the following:
```
  def home
    @sponsors_current = Sponsor.where('current=?', true)
  end
```
* Edit the file app/views/static_pages/home.html.erb.  Add the following code to the bottom of the page:
```
<h2>Current Sponsors</h2>
<div class="sponsors_current">
  <% @sponsors_current.each do |s| %>
    <li>
      <%= link_to s.name, s %>
    </li>
  <% end %>
</div>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor index capability (view level)"
git push origin 03-03-sponsor_index
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
