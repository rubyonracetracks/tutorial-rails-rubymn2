# Unit 1
## Chapter 2: Removing Pages

In this chapter, you will remove the about and contact pages.  However, this task is NOT as simple as simply deleting these two pages.  You must also remove tests, links in the header, controller methods, and routes.  Because of the ramifications of removing the about and contact pages is more complex than expected, this task gets its own chapter and is divided into multiple stages.

### New Branch
Enter the command "git checkout -b 01-02-remove_pages".

### Removing the About Link From the Header
* Edit the file app/views/layouts/_header.html.erb.  Remove the line containing "about_path".  This removes the link to the "About" page in the header menu bar.
* Enter the command "sh test_app.sh".  Two of the tests fail.  (NOTE: As of 1-28-2018, version 5.11.* of the minitest gem gives a traceback error if there are any errors.  This cuts off the rest of the test results.  Downgrading the minitest gem to version 5.10.3 is necessary to reinstate normal behavior.)
* Enter the command "alias test1='command for running failed tests minus the TESTOPTS portion'".
* Enter the command "test1".  Both tests still fail because the "About" link is no longer on the home page and contact page.
* Edit the file test/integration/static_pages_test.rb.  Remove the "home page provides access to the about page" test and the "contact page provides access to the about page"
* Enter the command "test1".  All tests should now pass.

### Removing the Contact Link From the Header.
* Edit the file app/views/layouts/_header.html.erb.  Remove the line containing "contact_path".  This removes the link to the "Contact" page in the header menu bar.
* Enter the command "test1".  Two tests fail because the "Contact" link is no longer on the home page and about page.
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
* Enter the command "test1".  One test fails because the about page lacks the expected content.
* In the test/integration/static_pages_test.rb file, remove the "about page has expected content" test.
* Enter the command "test1".  All tests should now pass.

### Blanking Out the Contact Page
* Enter the following commands:
```
rm app/views/static_pages/contact.html.erb
touch app/views/static_pages/contact.html.erb
```
* Enter the command "test1".  You will get a cascade of error messages because the contact page lacks the expected content.
* In the test/integration/static_pages_test.rb file, remove the "contact page has expected content" test.
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Removed all content from the About and Contact pages"
```

### Removing the About Page
* Enter the following command:
```
rm app/views/static_pages/about.html.erb
```
* Enter the command "test1".  One test fails because the about page has no template.
* In the config/routes.rb file, remove the line containing "get '/about'".
* Enter the command "test1".  One test fails because about_path is undefined in an integration test.
* In the test/integration/static_pages_test.rb file, remove the "about page provides access to the home page" test.
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh testc.sh". One test fails because about_path is undefined in the controller test.
* In the test/controllers/static_pages_controller_test.rb file, remove the "should get about" test.
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, but Rails Best Practices flags the controller for having an unused about method.
* In the app/controllers/static_pages_controller.rb file, remove the definition of "about".
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Removed the About page"
```

### Removing the Contact Page
* Enter the following command:
```
rm app/views/static_pages/contact.html.erb
```
* Enter the command "test1".  One test fails because the contact page has no template.
* In the config/routes.rb file, remove the line containing "get '/contact'".
* Enter the command "test1".  One test fails because contact_path is undefined in an integration test.
* In the test/integration/static_pages_test.rb file, remove the "contact page provides access to the home page" test.
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh testc.sh".  One test fails because contact_path is undefined in the controller test.
* In the test/controllers/static_pages_controller_test.rb file, remove the "should get contact" test.
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, but Rails Best Practices flags the controller for having an unused contact method.
* In the app/controllers/static_pages_controller.rb file, remove the definition of "contact".
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Removed the Contact page"
```
### Wrapping Up
* Enter the following command:
```
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
