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
* Enter the command "rails generate integration_test sponsor_delete".
* In the file test/integration/sponsor_delete_test.rb, replace everything between the line "class SponsorDeleteTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def check_no_delete_button
    visit sponsor_path(@sponsor1)
    assert page.has_no_link?('Delete Sponsor', href: sponsor_path(@sponsor1))
  end

  test 'visitor may not delete sponsor' do
    check_no_delete_button
  end

  test 'user may not delete sponsor' do
    login_as(@u1, scope: :user)
    check_no_delete_button
  end

  test 'regular admin may not delete sponsor' do
    login_as(@a4, scope: :admin)
    check_no_delete_button
  end

  test 'super admin gets button to delete sponsor' do
    login_as(@a1, scope: :admin)
    visit sponsor_path(@sponsor1)
    assert page.has_link?('Delete Sponsor', href: sponsor_path(@sponsor1))
  end

  test 'super admin can successfully delete sponsors' do
    login_as(@a1, scope: :admin)
    visit sponsor_path(@sponsor1)

    assert_difference 'Sponsor.count', -1 do
      click_on 'Delete Sponsor'
    end

    assert page.has_link?('Sky Store', href: sponsor_path(@sponsor2))
    assert page.has_link?('Island Hoppers', href: sponsor_path(@sponsor3))
    assert page.has_link?('Foundation for Law and Government', href: sponsor_path(@sponsor4))
  end
  ```
  * Enter the command "sh test_app.sh".  The last 2 new integration tests fail.
  * Enter the command "alias test1='(command to run failed tests minus the TESTOPTS portion)'".
  * Enter the command "test1".  The same 2 tests fail again because the "Delete Sponsor" button is missing.

#### Delete Sponsor Button
* Edit the file app/views/sponsors/show.html.erb. In the section for the edit and delete sponsor buttons, add the following code immediately after the "Edit Sponsor" button:
```
  <%= link_to "Delete Sponsor", @sponsor, method: :delete,
                            data: { confirm: "Are you sure you wish to delete this sponsor?" },
                            class: "btn btn-lg btn-primary"
  %>
  <br>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

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
