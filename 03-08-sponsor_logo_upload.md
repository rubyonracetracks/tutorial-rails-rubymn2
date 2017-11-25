# Unit 3
## Chapter 8: Uploading Sponsor Logos

In this chapter, you will enable super admins to upload a logo when creating or editing a sponsor.

### New Branch
Enter the command "git checkout -b 03-08-sponsor_logo_upload".

### Downloading Sponsor Logos
* Download the Debian Linux logo image by entering the following LONG one-line command:
```
curl -o app/assets/images/debian_logo.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/debian_logo.png
```
* Download the Best Buy logo image by entering the following LONG one-line command:
```
curl -o app/assets/images/Best_Buy_Logo.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/Best_Buy_Logo.png
```

### Integration Test
* Enter the command "rails generate integration_test sponsor_logo_upload".
* Edit the test/integration/sponsor_logo_upload_test.rb file.  Replace everything between the line "class SponsorLogoUploadTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```

```


### Wrapping Up
* Enter the command "git push origin 03-08-sponsor_logo_upload".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
