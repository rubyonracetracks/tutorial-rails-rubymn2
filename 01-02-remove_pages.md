# Unit 1
## Chapter 2: Removing Pages

In this chapter, you will remove the about and contact pages.  However, this task is NOT as simple as simply deleting these two pages.  You must also remove tests, links in the header, controller methods, and routes.  Because of the ramifications of removing the about and contact pages, this task gets its own chapter.

### New Branch
Enter the command "git checkout -b 01-02-remove_pages".

### Updating the Header
* Edit the file app/views/layouts/_header.html.erb.  Remove the lines containing "about_path" and "contact_path".  This removes the links to the "About" and "Contact" pages in the header menu bar.
* In your web browser view of your local version of the app, hit the refresh button.  The links to the "About" and "Contact" pages in the header should be gone.
* Enter the command "sh test_app.sh".  4 tests in test/integration/static_pages_test.rb fail.
* Enter the command "alias test1='(command for running the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  These 4 static page tests fail again.
* In the test/integration/static_pages_test.rb file, remove the "home page provides access to the about page" test.  Enter the command "test1".  3 of the tests should fail.
* In the test/integration/static_pages_test.rb file, remove the "home page provides access to the contact page" test.  Enter the command "test1".  2 of the tests should fail.
* In the test/integration/static_pages_test.rb file, remove the "about page provides access to the contact page" test.  Enter the command "test1".  1 of the tests should fail.
* In the test/integration/static_pages_test.rb file, remove the "contact page provides access to the about page" test.  Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Blanking Out the About and Contact Pages
* Enter the following commands to remove all contents from the about and contact pages and leaving blank pages in their place:
```
rm app/views/static_pages/about.html.erb
touch app/views/static_pages/about.html.erb
rm app/views/static_pages/contact.html.erb
touch app/views/static_pages/contact.html.erb
```
* Enter the command "test1".  2 tests in test/integration/static_pages_test.rb fail.
* In the test/integration/static_pages_test.rb file, remove the "about page has expected content" test. Enter the command "test1".  1 test will fail.
* In the test/integration/static_pages_test.rb file, remove the "contact page has expected content" test.  Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Removing the About and Contact Pages
* Enter the following commands to remove the about and contact pages:
```
rm app/views/static_pages/about.html.erb
rm app/views/static_pages/contact.html.erb
```
* Enter the command "test1".  2 tests should fail.  Both test failures are due to missing templates.
* In the config/routes.rb file, remove the line containing "get '/about'" and the line containing "get '/contact'".
* In the app/controllers/static_pages_controller.rb file, remove the definitions of the about and contact methods.
* Enter the command "test1".  Both tests fail because the "about" and "contact" actions are missing for the static pages controller.
* Edit the file test/controllers/static_pages_controller_test.rb.  Remove the "should get about" and "should get contact" tests.
* In the test/integration/static_pages_test.rb file, remove the "about page provides access to the home page" test and the "contact page provides access to the home page" test.  Enter the command "test1".  The single remaining static pages integration test should now pass.
* Enter the command "sh test_app.sh".  Now 3 tests fail, and those tests are in test/integration/user_edit_test.rb and test/integration/admin_edit_test.rb.  (NOTE: In the future, the app created with GenericApp will eliminate these tests.)
* In test/integration/user_edit_test.rb, edit the "user can access the page for editing settings" test.  Remove the line "visit about_path" and everything else in the test after this line.
* In test/integration/admin_edit_test.rb, edit the "super admin can access the page for editing settings" test and "regular admin can access the page for editing settings" test.  Remove the line "visit about_path" and everything else in these tests after this line.
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
