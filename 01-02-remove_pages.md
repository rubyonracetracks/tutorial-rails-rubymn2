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
* Enter the command "test1".  Now you get bombarded with a cascade of error messages because the "About" link is missing.
* In the test/integration/static_pages_test.rb file, remove the "home page provides access to the about page" test and the "contact page provides access to the about page" test.
* Enter the command "test1".  All tests should now pass.

### Removing the Contact Link From the Header.
* Edit the file app/views/layouts/_header.html.erb.  Remove the line containing "contact_path".  This removes the links to the "Contact" page in the header menu bar.
* Enter the command "test1".  Now you get bombarded with a cascade of error messages because the "Contact" link is missing.
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
* Enter the command "test1".  You will get a cascade of error messages because the "About" page lacks the expected content.
* In the test/integration/static_pages_test.rb file, remove the "about page has expected content" test.
* Enter the command "test1".  All tests should now pass.

### Blanking Out the Contact Page
* Enter the following commands:
```
rm app/views/static_pages/contact.html.erb
touch app/views/static_pages/contact.html.erb
```
* Enter the command "test1".  You will get a cascade of error messages because the "Contact" page lacks the expected content.
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
* Enter the command "test1".  You will get a cascade of error messages because the About page has no template.
* In the config/routes.rb file, remove the line containing "get '/about'".
* Enter the command "test1".  You will get a cascade of error messages because about_path is undefined in the integration test.
* In the test/integration/static_pages_test.rb file, remove the "about page provides access to the home page" test.
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh testc.sh".  You will get a cascade of error messages because about_path is undefined in the controller test.
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
* Enter the command "test1".  You will get a cascade of error messages because the Contact page has no template.
* In the config/routes.rb file, remove the line containing "get '/contact'".
* Enter the command "test1".  You will get a cascade of error messages because contact_path is undefined in the integration test.
* In the test/integration/static_pages_test.rb file, remove the "contact page provides access to the home page" test.
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh testc.sh".  You will get a cascade of error messages because contact_path is undefined in the controller test.
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
