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
* Enter the following commands to remove all contents from the about and contact pages and leave blank pages in their place:
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
* Enter the command "test1".  2 tests should fail.  Both test failures are due to missing templates.  (This is the result of removing the about and contact pages.)
* In the config/routes.rb file, remove the line containing "get '/about'" and the line containing "get '/contact'".  Enter the command "test1".  Now the 2 tests fail because the about_path and contact_path variables are undefined.
* In test/integration/static_pages_test.rb file, remove the "about page provides access to the home page" test and the "contact page provides access to the home page" test.  The ONLY test that should remain in this file is "home page has expected content".  Enter the command "test1".  All tests should now pass.
* Enter the command "sh test_app.sh".  5 of the tests will fail.  2 of the tests are in test/controllers/static_pages_controller_test.rb, 2 of the tests are in test/integration/admin_edit_test.rb, and 1 test is in test/integration/user_edit_test.rb.
* NOTE: In the future, the template app used by GenericApp will be revised to eliminate the portions of the tests in test/integration/admin_edit_test.rb and test/integration/user_edit_test.rb that depend on the "About" page.

### Updating the Admin Edit Test and User Edit Test
* In test/integration/user_edit_test.rb, edit the "user can access the page for editing settings" test.  Remove the line "visit about_path" and everything else in the test after this line.
* In test/integration/admin_edit_test.rb, edit the "super admin can access the page for editing settings" test and "regular admin can access the page for editing settings" test.  Remove the line "visit about_path" and everything else in these tests after this line.
* Enter the command "sh test_app.sh".  Now the 2 failed tests in test/controllers/static_pages_controller_test.rb are the only ones that should remain.

### Updating the Static Pages Controller
* Enter the command "sh testc.sh".  2 tests will fail.  One test will fail because the variable about_path is undefined, and the other test will fail because the variable contact_path is undefined.
* Edit the file test/controllers/static_pages_controller_test.rb.  Remove the "should get about" and "should get contact" tests.  Only the "should get home" test should remain.
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should now pass, but Rails Best Practices now flags the unused about and contact methods in app/controllers/static_pages_controller.rb.
* Edit the file app/controllers/static_pages_controller.rb.  Remove the definitions of the "about" and "contact" methods.  Only the "home" definition should remain.
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
