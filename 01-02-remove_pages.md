# Unit 1
## Chapter 2: Removing Pages

In this chapter, you will remove the about and contact pages.  However, this task is NOT as simple as simply deleting these two pages.  You must also remove tests, links in the header, controller methods, and routes.  Because of the ramifications of removing the about and contact pages, this task gets its own chapter.

### New Branch
Enter the command "git checkout -b 01-02-remove_pages".

### Removing the About Link From the Header
* Enter the command "sh test_app.sh".  All tests should pass.
* Enter the command "alias test1='rails test test/integration/static_pages_test.rb'".
* Enter the command "test1".  This tests the static page integration tests only.  All tests should pass.
* Edit the file app/views/layouts/_header.html.erb.  Remove the line containing "about_path".  This removes the links to the "About" page in the header menu bar.
* Enter the command "test1".  Now you get bombarded with a cascade of error messages.
* In the test/integration/static_pages_test.rb file, remove the "home page provides access to the about page" test and the "contact page provides access to the about page" test.
* Enter the command "test1".  All tests should now pass.

### Removing the Contact Link From the Header.
* Edit the file app/views/layouts/_header.html.erb.  Remove the line containing "contact_path".  This removes the links to the "Contact" page in the header menu bar.
* Enter the command "test1".  Now you get bombarded with a cascade of error messages.
* In the test/integration/static_pages_test.rb file, remove the "home page provides access to the contact page" test and the "about page provides access to the contact page" test.
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Removed the links to the About and Contact pages in the header"
```

### Blanking Out the About Page
* Enter the following commands:
```
rm app/views/static_pages/about.html.erb
touch app/views/static_pages/about.html.erb
```
* Enter the command "test1".  You will get a cascade of error messages.
* In the test/integration/static_pages_test.rb file, remove the "about page has expected content" test.
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh test_app.sh".  All tests should now pass.

### Blanking Out the Contact Page
* Enter the following commands:
```
rm app/views/static_pages/contact.html.erb
touch app/views/static_pages/contact.html.erb
```
* Enter the command "test1".  You will get a cascade of error messages.
* In the test/integration/static_pages_test.rb file, remove the "contact page has expected content" test.
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.


### Removing the About Page
* Enter the following command:
```
rm app/views/static_pages/about.html.erb
```
* Enter the command "test1".  You will get a cascade of error messages.
* In the test/integration/static_pages_test.rb file, remove the "about page provides access to the home page" test.
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh test_app.sh".  All tests should now pass.

### Removing the Contact Page
* Enter the following command:
```
rm app/views/static_pages/contact.html.erb
```
* Enter the command "test1".  You will get a cascade of error messages.
* In the test/integration/static_pages_test.rb file, remove the "contact page provides access to the home page" test.  At this point, the only remaining test in this file should be the "home page has expected content" test.
* Enter the command "test1".  All tests should now pass.









* In the config/routes.rb file, remove the line containing "get '/about'" and the line containing "get '/contact'".  Enter the command "test1".  Now the 2 tests fail because the about_path and contact_path variables are undefined.
* In test/integration/static_pages_test.rb file, remove the "about page provides access to the home page" test and the "contact page provides access to the home page" test.  The ONLY test that should remain in this file is "home page has expected content".  Enter the command "test1".  All tests should now pass.
* Enter the command "sh test_app.sh".  2 of the tests will fail.  Both of the tests are in test/controllers/static_pages_controller_test.rb.

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
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
