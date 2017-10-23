# Unit 1
## Chapter 5: Updating the Styling

In this chapter, you will update the styling of your app.

### New Branch
Enter the command "git checkout -b 01-05-styling".

### Updating the Navigation Bar
* Change the color of the navigation bar from the default black color to dark red.  In the file app/assets/stylesheets/custom.scss, add the following lines to the end of the header section:
```
.navbar-inverse {
  background-color: #660000;
}
```
* Refresh your web browser's view of the local version of your site.

### Updating the Color of Links in the Navigation Bar
* Unfortunately, the gray text in the navigation bar is less readable with the dark red background than it was with the white background.  The solution is to make the text in the navigation bar always white.
* Making the text in the navigation bar always white means that the user does not clearly see that it's possible to click on the item and go to a different page on the site.  The solution is to change the background immediately around the text to black.  This makes it much more apparent that clicking on the text leads to a different page.
* In the file app/assets/stylesheets/custom.scss, add the following lines to the end of the header section:
```
.navbar .nav>li>a {
  color: #FFFFFF;
  &:hover {
    background-color: #000000;
  }
}
```

### Highlighting the Selected Link in the Navigation Bar
Change the background of the selected link instead of the text.

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
