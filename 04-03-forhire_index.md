# Unit 4
## Chapter 3: Forhire Index

In this chapter, you will add the forhire index.

### New Branch
Enter the command "git checkout -b 04-03-forhire_index".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/forhires_controller_test.rb.  Add the following lines before the last "end" statement:
```
  test 'forhire index action' do
    get forhires_path
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  Your new controller test fails because forhires_path is undefined.

#### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :forhires" with the following:
```
  resources :forhires, only: [:show, :index]
```
* Enter the command "sh testc.sh".  The test fails because the index action is not defined in the forhire controller.

#### Controller
* Edit the file app/controllers/forhires_controller.rb.  Just before the end of the action section, add the following lines:
```
  def index
    @forhires = Forhire.order('updated_at DESC').paginate(page: params[:page])
  end
```
* Enter the command "sh testc.sh".  The test fails because of a missing template.

#### Blank View
* Enter the command "touch app/views/forhires/index.html.erb".  Leave this file blank for now.  (You'll fill it in soon.)
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the forhire index capability (controller level)"
```

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test forhire_index".
* Edit the file test/integration/forhire_index_test.rb.  Replace everything between "class ForhireIndexTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  test 'The forhire index page has the expected content' do
    visit forhires_path
    assert page.has_css?('title', text: full_title('For Hire Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'For Hire Index')
    assert page.has_link?('James Bond 1962-1971', href: forhire_path(@fh_connery))
    assert page.has_link?('James Bond 1969', href: forhire_path(@fh_lazenby))
    assert page.has_link?('James Bond 1973-1985', href: forhire_path(@fh_moore))
    assert page.has_link?('James Bond 1987-1989', href: forhire_path(@fh_dalton))
    assert page.has_link?('James Bond 1995-2002', href: forhire_path(@fh_brosnan))
    assert page.has_link?('James Bond 2006-', href: forhire_path(@fh_craig))

    assert_text 'Sean Connery'
    assert_text 'I stopped Blofeld 4 times!'
    assert_text 'George Lazenby'
    assert_text 'I was James Bond for one movie.'
    assert_text 'Roger Moore'
    assert_text 'I made James Bond campier.'
    assert_text 'Timothy Dalton'
    assert_text 'I brought gritty realism to the Bond series.'
    assert_text 'Pierce Brosnan'
    assert_text 'James Bond moved beyond the Cold War Era on my watch.'
    assert_text 'Daniel Craig'
    assert_text 'I rebooted James Bond.'
  end

  test 'The header provides access to the forhire index page' do
    visit root_path
    assert page.has_link?('For Hire', href: forhires_path)
  end
```
* Enter the command "sh test_app.sh".  Your 2 new integration tests fail.
* Enter the command "alias test1='(command to run the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same 2 integration tests fail again because the expected content is not present.

#### Forhire Index Page
* Fill in the blank app/views/forhires/index.html.erb file with the following code:
```
<% provide(:title, 'For Hire Index') %>

<h1>For Hire Index</h1>

<%= will_paginate %>

<%= render @forhires %>
<hr>

<%= will_paginate %>
```
* Create the file app/views/forhires/_forhire.html.erb with the following content:
```
<% require 'email_munger' %>

<hr>
<% user_f = User.where(id: "#{forhire.user_id}").first %>
<%= user_f.first_name %> <%= user_f.last_name %>
<br>
<%= link_to forhire.title, forhire %>, <%= raw(EmailMunger.encode(forhire.email)) %>
<br>
<%= forhire.description[0..140] %>
```
* Enter the command "test1".  Now only one test fails.  The forhire index page has the expected content, but the link to this page from the home page has not yet been added.

#### Header
* Edit the file app/views/layouts/_header.html.erb.  Just before the beginning of the variable section, add the following line:
```
        <li><%= link_to "For Hire", forhires_path %></li>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the forhire index capability (view level)"
git push origin 04-03-forhire_index
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
