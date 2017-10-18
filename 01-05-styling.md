# Unit 1
## Chapter 5: Updating the Styling

In this chapter, you will add images to your app's layout.

### New Branch
Enter the command "git checkout -b 01-05-styling".

### Removing A Legacy Image
* Remove the Rails logo at the bottom of the home page, which looks cluttered.
* Enter the command "rm app/assets/images/rails.png".
* In the app/views/static_pages/home.html.erb file, remove all code between the Twitter image and the final "</div>".  This removes the Rails logo.

### Header Image
* Download the header background image by entering the following LONG one-line command:
```
curl -o app/assets/images/header_background.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/header_background.png
```
* Edit the file app/views/layouts/_header.html.erb.  Replace the line containing 'link_to "Ruby Users of Minnesota"' with the following:
```
    <%= image_tag 'header_background.png' %>
```
* In your web browser showing the local version of your app, hit refresh.  Now the header blocks the top of the page.
* To correct this, edit the file app/assets/stylesheets/custom.scss.  Under the body parameter, change the padding-top value to 260.
* In your web browser showing the local version of your app, hit refresh.

### Background Color


### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Updating the styling"
git push origin 01-05-styling
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
