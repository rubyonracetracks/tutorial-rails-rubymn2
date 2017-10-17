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

### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
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
