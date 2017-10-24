# Unit 1
## Chapter 3: Updating the Static Pages Text

In this chapter, you will update the content of the home page and footer.

### New Branch
Enter the command "git checkout -b 01-03-static_text".

### Removing "by Somebody"
* In the app/views/layouts/_footer.html.erb file, remove "by Somebody".  (It's a boilerplate placeholder that you'd replace with your own name.  Since Ruby.MN is a team effort, let's just leave it out.)
* Enter the command "sh test_app.sh".  One test fails.
* Enter the command "alias test1='(Command to run failed test minus the TESTOPTS portion)'".
* Enter the command "test1".  The test that fails specifies that the text "by Somebody" be present.
* In the test/integration/static_pages_test.rb file, replace "Ruby Users of Minnesota by Somebody" with "Ruby Users of Minnesota".
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Adding Integration Tests
* In the test/integration/static_pages_test.rb file, add the following lines to the end of the "home page has expected content" test:
```
    assert page.has_css?('small', text: "#{Time.now.year}")
    assert page.has_link?('Heroku', href: 'https://www.heroku.com')
    assert page.has_text?('We are Ruby enthusiasts from the Twin Cities area.')
    assert page.has_link?('https://www.meetup.com/ruby-mn', href: 'https://www.meetup.com/ruby-mn')
    assert page.has_link?('https://twitter.com/rubymn', href: 'https://twitter.com/rubymn')
    assert page.has_link?('https://groups.google.com/forum/#!forum/rubymn', href: 'https://groups.google.com/forum/#!forum/rubymn')
    assert page.has_link?('https://www.dayblockbrewing.com', href: 'https://www.dayblockbrewing.com')
```
* Enter the command "test1".  The test fails because the copyright statement is missing.

### Adding to the Footer
* In the app/views/layouts/_footer.html.erb file, replace the line containing "Ruby Users of Minnesota" with the following lines:
```
    &copy; <%= "#{Time.now.year}" %>
    <%= link_to "Ruby Users of Minnesota", root_path %>
    <br><br>
    Hosting provided by <%= link_to "Heroku", "https://www.heroku.com" %>
```
* Enter the command "test1".  Now the test fails because the text "We are Ruby enthusiasts from the Twin Cities area" is missing from the home page.

### Adding to the Home Page
* In the app/views/static_pages/home.html.erb page, add the following lines after the end of the variable section:
```
<h2>About</h2>
We are Ruby enthusiasts from the Twin Cities area.
On this site, we showcase sponsors, post job openings, showcase our projects, and present our qualifications.
We meet on the last Monday of the month at 6:30PM at Day Block Brewing.
You can RSVP to our events on our Meetup page, read our Tweets on our Twitter page, and discuss Ruby on our Google group.

<h2>Links</h2>
<ul>
  <li>Meetup:<%= link_to "https://www.meetup.com/ruby-mn", "https://www.meetup.com/ruby-mn"</li>
  <li>Twitter:<%= link_to "https://twitter.com/rubymn", "https://twitter.com/rubymn"</li>
  <li>Google group:<%= link_to "https://groups.google.com/forum/#!forum/rubymn", https://groups.google.com/forum/#!forum/rubymn"</li>
  <li>Day Block Brewing:<%= link_to "https://www.dayblockbrewing.com", "https://www.dayblockbrewing.com"</li>
</ul>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Updated the text in the static pages"
git push origin 01-03-static_text
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
