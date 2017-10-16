# Unit 1
## Chapter 2: Removing Pages

In this chapter, you will remove the about and contact pages.  However, this task is NOT as simple as simply deleting these two pages.  You must also remove tests, links in the header, controller methods, and routes.  Because of the ramifications of removing the about and contact pages, this task gets its own chapter.

### New Branch
Enter the command "git checkout -b 01-02-remove_pages".

### Updating the Header
* Edit the file app/views/layouts/_header.html.erb.  Remove the lines containing "about_path" and "contact_path".
* In your web browser view of your local version of the app, hit the refresh button.  The links to the "About" and "Contact" pages in the header should be gone.
* Enter the command "sh test_app.sh".

### Blanking Out the About and Contact Pages
* Enter the following commands:
```
rm app/views/static_pages/about.html.erb
touch app/views/static_pages/about.html.erb
rm app/views/static_pages/contact.html.erb
touch app/views/static_pages/contact.html.erb
```
* The net result of this is replacing the about and contact pages with blank pages.
* Enter the command "sh test_app.sh".  6 tests in test/integration/static_pages_test.rb fail.
* Enter the command "alias test1='(command for running the failed tests minus the TESTOPTS portion'".
* Enter the command "test1".  These 6 static page tests fail again.
* In the test/integration/static_pages_test.rb file, remove the "about page has expected content" test. Enter the command "test1".  5 tests will fail.
* In the test/integration/static_pages_test.rb file, remove the "contact page has expected content" test.  Enter the command "test1".  4 tests will fail.
* In the test/integration/static_pages_test.rb file, remove the "home page provides access to the about page" test.  Enter the command "test1".  3 tests will fail.
* In the test/integration/static_pages_test.rb file, remove the "home page provides access to the contact page" test.  Enter the command "test1".  2 tests will fail.
* In the test/integration/static_pages_test.rb file, remove the "about page provides access to the contact page" test.  Enter the command "test1".  1 test will fail.
* In the test/integration/static_pages_test.rb file, remove the "contact page provides access to the about page" test.  Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Removed the about and contact pages"
git push origin 01-02-remove_pages
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
