# Unit 4
## Chapter 3: Forhire Index

In this chapter, you will add the forhire index.

### New Branch
Enter the command "git checkout -b 04-03-forhire_index".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/forhires_controller_test.rb.  Add the following lines before the last "end" statement:
```
  test 'forhire index action' do
    get forhires_path
    assert_response :success
  end
```
#### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :forhires" with the following:
```
  resources :forhires, only: [:show, :index]
```

#### Controller
* Edit the file app/controllers/forhires_controller.rb.  Just before the end of the action section, add the following lines:
```
  def index
    @forhires = Forhire.paginate(page: params[:page])
  end
```

#### Blank View
* Enter the command "touch app/views/forhires/index.html.erb".
* Enter the command "sh testc.sh".  All tests should now pass.

### Part B: View Level

### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the forhire index capability"
git push origin 04-03-forhire_index
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
