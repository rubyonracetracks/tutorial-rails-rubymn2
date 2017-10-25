# Unit 2
## Chapter 1: User Profiles

In this chapter, you will allow users to view the profiles of other users.

### New Branch
Enter the command "git checkout -b 02-01-user_show".

### test/controllers/users_controller_test.rb
* Edit the file test/controllers/users_controller_test.rb.  Replace everything within the show-other_user section with the following:
```
  test 'should not redirect users from the profiles of other users' do
    sign_in @u1, scope: :user
    get user_path(@u2)
    assert :success
    get user_path(@u3)
    assert :success
    get user_path(@u4)
    assert :success
    get user_path(@u5)
    assert :success
    get user_path(@u6)
    assert :success
    get user_path(@u7)
    assert :success
  end
```

### Integration Test

### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Made user profiles public"
git push origin 02-01-user_show
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
