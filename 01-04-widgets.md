# Unit 1
## Chapter 4: Adding Widgets

In this chapter, you will add widgets displaying Meetup group information and the latest Tweets.

### New Branch
Enter the command "git checkout -b 01-04-widgets".

### Meetup Widget
* In the app/views/static_pages/home.html.erb page, add the following lines after the description of the Ruby.MN group:
```
  <br><br>
  <% # BEGIN: MEETUP WIDGET %>
  <iframe width="200" height="420" src="http://meetu.ps/2Rx1rR" frameborder="0"></iframe>
  <% # END: MEETUP WIDGET %>
```
* In your browser, refresh the local version of your app.  The Meetup widget should now appear.


### Twitter Widget
* In the app/views/static_pages/home.html.erb page, add the following lines after the Meetup Widget:
```
  <% # BEGIN: TWITTER WIDGET %>
  <a class="twitter-timeline" data-width="420" data-height="420" href="https://twitter.com/RubyMN?ref_src=twsrc%5Etfw">Tweets by RubyMN</a> <script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
  <% # END: TWITTER WIDGET %>
```
* In your browser, refresh the local version of your app.  The Twitter widget should now appear.

### Home Page
* In the test/integration/static_pages_test.rb file, add the following lines to the end of the "home page has expected content" test.
```
    assert page.has_link?('ruby.mn Google group', href: 'https://groups.google.com/forum/#!forum/rubymn')
    assert page.has_link?('Ruby.MN Meetup group', href: 'https://www.meetup.com/ruby-mn/')
    assert page.has_link?('Ruby.MN on Twitter', href: 'https://twitter.com/rubymn')
```
* Enter the command "sh test_app.sh".  One of the tests fails because the home page does not have all of the expected content.
* Enter the command "alias test1='(command for running failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The test still fails.
* In the app/views/static_pages/home.html.erb page, add the following lines after the end of the variable section:
```
We are Ruby enthusiasts from the Twin Cities area.  On this site, we showcase sponsors, post job openings, showcase our projects, and present our qualifications.  We meet on the last Monday of the month at <%= link_to 'Day Block Brewery', 'https://www.dayblockbrewing.com/' %>.  For more details, go to the <%= link_to 'Ruby.MN Meetup group', 'https://www.meetup.com/ruby-mn/' %> to register to attend the next meeting.  Our <%= link_to 'ruby.mn Google group', 'https://groups.google.com/forum/#!forum/rubymn' %> is the primary way to discuss Ruby with our members.  To view our full Twitter feed, go to <%= link_to 'Ruby.MN on Twitter', 'https://twitter.com/rubymn' %>.
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added widgets to the home page."
git push origin 01-04-widgets
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
