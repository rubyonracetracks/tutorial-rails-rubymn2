# Unit 3
## Chapter 8: Editing Sponsors in the Controller
In this chapter, you will give super admins the ability to edit sponsor information at the controller level.  Again, only super admins may do this.

### New Branch
Enter the command "git checkout -b 03-08-sponsor_edit_controller".

### Controller Test
* Edit the file test/controllers/sponsors_controller_test.rb.  Add the following code immediately after the definition of create_sponsor_disabled:
```
  def edit_sponsor
    patch sponsor_path(@sponsor1),
          params: { sponsor: { description: 'parody of Best Buy' } }
  end

  def edit_sponsor_disabled
    create_sponsor
    assert_redirected_to root_path
  end
```
* Edit the file test/controllers/sponsors_controller_test.rb.  Add the following code just before the last "end" statement:
```
  test 'should redirect edit when not logged in' do
    create_sponsor_disabled
  end

  test 'should redirect edit when logged in as a user' do
    sign_in @u1, scope: :user
    create_sponsor_disabled
  end

  test 'should redirect edit when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    create_sponsor_disabled
  end

  test 'should not redirect edit when logged in as a super admin' do
    sign_in @a1, scope: :admin
    edit_sponsor
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  The last test fails because of a missing route.

### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :sponsors" with the following:
```
  resources :sponsors, only: [:show, :index, :create, :new, :update, :edit]
```
* Enter the command "sh testc.sh".  The test fails because the update action is not provided in the sponsor controller.

### Sponsor Controller
* In app/controllers/sponsors_controller.rb, replace the before_action statement with the following:
```
  # rubocop:disable Style/SymbolArray
  before_action :may_control_sponsor, only: [:create, :update]
  # rubocop:enable Style/SymbolArray
```
* Just before the end of the action section in app/controllers/sponsors_controller.rb, add the following code:
```
  def update
    @sponsor = Sponsor.find(params[:id])
    if @sponsor.update_attributes(sponsor_params)
      flash[:success] = 'Sponsor updated'
    else
      render 'edit'
    end
  end

  def edit
    @sponsor = Sponsor.find(params[:id])
  end
```
* Enter the command "sh testc.sh".  All tests should now pass.
* ENter the command "sh git_check.sh".  All tests should now pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor controller edit action"
git push origin 03-08-sponsor_edit_controller
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh". 
