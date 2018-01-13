# Unit 1
## Chapter 4: Adding Footer Images

In this chapter, you will add footer and header images.

### New Branch
Enter the command "git checkout -b 01-04-images".

### Removing the Rails Logo from the Home Page
* Remove the Rails logo graphic file by entering the command "rm app/assets/images/rails.png".
* Refresh your view of the local app's home page in your browser.  Now you'll get an error because the rails.png file is missing.
* Edit the file app/views/static_pages/home.html.erb.  Remove the lines containing the link_to element that uses the rails.png file you just deleted.
* Refresh your view of the local app in your browser.

### Adding the Logos to the Footer
* Download the Ruby.MN logo image by entering the following LONG one-line command:
```
curl -o app/assets/images/rumlogo.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/rumlogo.png
```
* Download the footer background image by entering the following LONG one-line command:
```
curl -o app/assets/images/footer_background.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/footer_background.png
```

* Edit the file app/views/layouts/_footer.html.erb.  Add the following just after the link to Heroku:
```
    <br><br>
    <%= image_tag 'rumlogo.png' %>
    <br>
    <%= image_tag 'footer_background.png' %>
```
* Refresh your view of the local app in your browser.  The Ruby.MN logo and the footer background image will now appear at the bottom of each page.

### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added footer images"
git push origin 01-04-images
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
