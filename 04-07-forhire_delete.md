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
* In the file test/controllers/forhires_controller_test.rb, add the following line immediately before "class ForhiresControllerTest < ActionDispatch::IntegrationTest":
```
# rubocop:disable Metrics/ClassLength
```
* In the file test/controllers/forhires_controller_test.rb, add the following line immediately after the last "end" statement:
```
# rubocop:enable Metrics/ClassLength
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because of a missing route.

#### Routing
* In the file config/routes.rb, replace the line containing "resources :forhires" with the following:
```
  resources :forhires, only: [:show, :index, :create, :new, :update, :edit, :destroy] do
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because the destroy action is not defined in the forhire controller.

#### Forhire Controller
* Edit the file app/controllers/forhires_controller.rb.  Add the following line just before the end of the before_action section:
```
  before_action :may_destroy_forhire, only: [:destroy]
```
* Just before the end of the action section in app/controllers/forhires_controller.rb, add the following code:
```
  def destroy
    uid = Forhire.find(params[:id]).user_id
    Forhire.find(params[:id]).destroy
    flash[:success] = 'For hire profile deleted'
    redirect_to user_path(uid)
  end
```
* Just before the definition of forhire_params, add the following code:
```
  def correct_user
    return false unless user_signed_in?
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
git commit -m "Added forhire delete (controller level)"
```

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test forhire_delete".
* In the file test/integration/forhire_delete_test.rb, replace everything between "class ForhireDeleteTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def no_delete_button
    visit forhire_path(@fh_lazenby)
    assert page.has_no_link?('Delete For Hire Profile', href: forhire_path(@fh_lazenby))
  end

  def gets_delete_button
    visit forhire_path(@fh_lazenby)
    assert page.has_link?('Delete For Hire Profile', href: forhire_path(@fh_lazenby))
  end

  def can_delete
    gets_delete_button
    assert_difference 'Forhire.count', -1 do
      click_on 'Delete For Hire Profile'
    end
    assert_text 'For hire profile deleted'
    assert page.has_css?('title', text: full_title('User: George Lazenby'),
                                  visible: false)
    assert page.has_css?('h1', text: 'User: George Lazenby',
                               visible: false)
  end

  test 'visitor does not get the delete button' do
    no_delete_button
  end

  test 'wrong user does not get the delete button' do
    no_delete_button
  end

  test 'right user gets the delete button' do
    gets_delete_button
  end

  test 'regular admin gets the delete button' do
    gets_delete_button
  end

  test 'super admin gets the delete button' do
    gets_delete_button
  end

  test 'right user can delete forhire' do
    can_delete
  end

  test 'regular admin can delete forhire' do
    can_delete
  end

  test 'super admin can delete forhire' do
    can_delete
  end
```
* Enter the command "sh test_app.sh".  The last 6 of your new integration tests fail.
* Enter the command "alias test1='command for running failed tests minus the TESTOPTS portion'".
* Enter the command "test1".  The same 6 tests fail because the delete button is missing.

#### Adding the Delete Button
* Edit the file app/views/forhires/show.html.erb. Immediately after the edit button, add the following code:
```
<% # BEGIN: delete forhire button %>
<%= link_to "Delete For Hire Profile", @forhire, method: :delete,
                                       data: { confirm: "Are you sure you wish to delete this for hire profile?" },
                                       class: "btn btn-danger"
%>
<br><br>
<% # END: delete forhire button %>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  

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
