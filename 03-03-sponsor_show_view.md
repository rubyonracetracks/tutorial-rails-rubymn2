# Unit 3
## Chapter 3: Sponsor Profiles

In this chapter, you will add sponsor profile pages.

### New Branch
Enter the command "git checkout -b 03-03-sponsor_show_view".

### Procedure
* Enter the command "rails generate integration_test sponsor_show".
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
* Edit the blank app/views/sponsors/show.html.erb file.  Give it the following content:
```
<% provide(:title, @sponsor.name) %>
<% if @sponsor.current == true %>
  <h1>Current Sponsor: <%= @sponsor.name %></h1>
<% else %>
  <h1>Past Sponsor: <%= @sponsor.name %></h1>
<% end %>
<br>
Email: <%= raw(EmailMunger.encode(@sponsor.contact_email)) %>
<br>
Phone: <%= @sponsor.phone %>
<br>
URL: <%= link_to @sponsor.contact_url, @sponsor.contact_url %>
<br><br>
<%= @sponsor.description %>
```
* Enter the command "test1".  All tests should pass.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor profile pages"
git push origin 03-03-sponsor_show_view
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
