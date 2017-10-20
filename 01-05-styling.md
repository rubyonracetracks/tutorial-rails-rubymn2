# Unit 1
## Chapter 5: Updating the Styling

In this chapter, you will update the styling of your app.

### New Branch
Enter the command "git checkout -b 01-05-styling".

### Navigation Bar Color
* Change the color of the navigation bar from the default black color to dark red.  In the file app/assets/stylesheets/custom.scss, add the following lines to the end of the header section:
```
.navbar-inverse {
  background-color: #660000;
}
```
* Refresh your web browser's view of the local version of your site.

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
