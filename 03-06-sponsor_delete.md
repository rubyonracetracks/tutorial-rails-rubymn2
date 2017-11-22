# Unit 3
## Chapter 6: Deleting Sponsors

In this chapter, you will give super admins the ability to delete sponsors.

### New Branch
Enter the command "git checkout -b 03-06-sponsor_delete".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/sponsors_controller_test.rb.  Add the following code immediately after the definition of edit_sponsor_disabled:
```
  def delete_sponsor
    delete sponsor_path(@sponsor1)
  end

  def delete_sponsor_disabled
    assert_no_difference 'Admin.count' do
      delete_sponsor
      assert_redirected_to root_path
    end
  end
```
* Edit the file test/controllers/sponsors_controller_test.rb.  Add the following code immediately before the last "end" statement:
```
  test 'should redirect delete when not logged in' do
    delete_sponsor_disabled
  end

  test 'should redirect delete when logged in as a user' do
    sign_in @u1, scope: :user
    delete_sponsor_disabled
  end

  test 'should redirect delete when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    delete_sponsor_disabled
  end

  test 'should not redirect delete when logged in as a super admin' do
    sign_in @a1, scope: :admin
    assert_difference 'Sponsor.count', -1 do
      delete_sponsor
    end
  end
```
* Enter the command "sh testc.sh".  All 4 new controller tests fail because of a missing route.

#### Routing
* In the file config/routes.rb, replace the line containing "resources :sponsors" with the following:
```
  resources :sponsors, only: [:show, :index, :create, :new, :update, :edit, :destroy]
```
* Enter the command "sh testc.sh".  All 4 new controller tests fail because the destroy action is not defined in the sponsor controller.

#### Sponsor Controller
* Edit the file app/controllers/sponsors_controller.rb.  Replace the line containing "before_action :may_control_sponsor" with the following:
```
  before_action :may_control_sponsor, only: [:create, :update, :destroy]
```
* Just before the end of the action section in app/controllers/sponsors_controller.rb, add the following code:
```
  def destroy
    Sponsor.find(params[:id]).destroy
    flash[:success] = 'Sponsor deleted'
    redirect_to(admins_path)
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added sponsor delete (controller level)"
```

### Part B: View Level

#### Integration Test

#### Sponsor Profile Page


### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added sponsor delete (view level)"
git push origin 03-06-sponsor_delete
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
