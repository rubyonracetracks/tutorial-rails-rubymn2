# Unit 4
## Chapter 4: For Hire Model

### New Branch
Enter the command "git checkout -b 04-01-forhire_model".

### Model Test
* Enter the following command:
```
rails generate model for_hire blurb:text email:string title:string user:references
```
* Note that the "user:references" portion of the above command connects this object to a user object.
* Enter the command "rails db:migrate".
* Edit the file test/models/for_hire_test.rb.  Replace everything between the line "class ForHireTest < ActiveSupport::TestCase" and the last "end" statement with the following:
```

```

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the for hire model"
git push origin 04-01-forhire_model
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
