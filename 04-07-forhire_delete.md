# Unit 4
## Chapter 7: Deleting For Hire Profiles

In this chapter, you will provide the ability to delete for hire profiles.  Only the owner of this object, a regular admin, or a super admin may have this ability.

### New Branch
Enter the command "git checkout -b 04-07-forhire_delete".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/forhires_controller_test.rb.  Add the following code immediately before the end of the definitions section:
```
  def delete_forhire
    delete forhire_path(@fh_lazenby)
  end

  def delete_forhire_disabled
    assert_no_difference 'Forhire.count' do
      delete_forhire
      assert_redirected_to root_path
    end
  end
```
* Edit the file test/controllers/forhires_controller_test.rb.  Add the following code immediately before the last "end" statement:
```
  test 'should redirect delete when not logged in' do
    delete_forhire_disabled
  end

  test 'should redirect delete when logged in as the wrong user' do
    sign_in @u1, scope: :user
    delete_forhire_disabled
  end

  test 'should not redirect delete when logged in as the right user' do
    sign_in @u2, scope: :user
    assert_difference 'Forhire.count', -1 do
      delete_forhire
      assert_redirected_to user_path(@u2)
    end
  end

  test 'should not redirect delete when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    assert_difference 'Forhire.count', -1 do
      delete_forhire
      assert_redirected_to user_path(@u2)
    end
  end

  test 'should not redirect delete when logged in as a super admin' do
    sign_in @a1, scope: :admin
    assert_difference 'Forhire.count', -1 do
      delete_forhire
      assert_redirected_to user_path(@u2)
    end
  end
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because of a missing route.

#### Routing
* In the file config/routes.rb, replace the line containing "resources :sponsors" with the following:
```
  resources :forhires, only: [:show, :index, :create, :new, :update, :edit, :destroy] do
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because the destroy action is not defined in the forhire controller.

#### Forhire Controller
* Edit the file app/controllers/forhires_controller.rb.  Add the following line just before the end of the before_action section:
```
  before_action :may_destroy_forhire, only: [:update]
```
* Just before the end of the action section in app/controllers/forhires_controller.rb, add the following code:
```
  def destroy
    Forhire.find(params[:id]).destroy
    flash[:success] = 'For hire profile deleted'
    redirect_to(forhires_path)
  end
```
* Just before the definition of forhire_params, add the following code:
```
  def correct_user
    current_user.id == Forhire.find(params[:id]).user_id
  end
  helper_method :correct_user

  def may_destroy_forhire
    return redirect_to(root_path) unless correct_user || admin_signed_in?
  end
  helper_method :may_destroy_forhire
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added sponsor delete (controller level)"
```

### Part B: View Level

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added forhire delete (view level)"
git push origin 04-07-forhire_delete
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
