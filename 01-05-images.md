# Unit 1
## Chapter 5: Adding Images

In this chapter, you will add images to your app's layout.

### New Branch
Enter the command "git checkout -b 01-05-images".

### Removing A Legacy Image
* Remove the Rails logo at the bottom of the home page, which looks cluttered.
* Enter the command "rm app/assets/images/rails.png".
* In the app/views/static_pages/home.html.erb file, remove all code between the Twitter image and the final "</div>".  This removes the Rails logo.

### Header Image
* Download the header background image by entering the following command:
```
curl -o app/assets/images/header_background.png -OL https://github.com/jhsu802701/tutorial_rails_rubymn2/blob/master/images/header_background.png
```

### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added images to the layout"
git push origin 01-05-images
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
