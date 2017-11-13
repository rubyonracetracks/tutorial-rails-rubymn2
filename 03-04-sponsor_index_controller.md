# Unit 3
## Chapter 4: Sponsor Index Controller

In this chapter, you will add a sponsor index controller.

### New Branch
Enter the command "git checkout -b 03-04-sponsor_index_controller".

### Controller Test
* Edit the file test/controllers/sponsors_controller_test.rb.  Add the following lines before the last "end" statement:
```
  test 'sponsor index action' do
    get sponsors_path
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  Your new sponsor controller test fails because sponsors_path is undefined.

### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :sponsors" with the following:
```
  resources :sponsors, only: [:show, :index]
```
* Enter the command "sh testc.sh".  The same test now fails because the index action is not provided by the sponsor controller.

### Controller
* Edit the file app/controllers/sponsors_controller.rb.  Just before the end of the action section, add the following lines:
```
  def index
    @sponsors_current = Sponsor.where('current=?', true)
    @sponsors_past = Sponsor.where('current!=?', true)
  end
```
* Enter the command "sh testc.sh".  The test fails because of a missing template.

### Blank View
* Enter the command "touch app/views/sponsors/index.html.erb".
* Enter the command "sh testc.sh".  All tests should now pass.

### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor index controller"
git push origin 03-04-sponsor_index_controller
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
