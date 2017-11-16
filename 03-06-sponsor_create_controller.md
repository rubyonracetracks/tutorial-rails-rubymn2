# Unit 3
## Chapter 6: Creating Sponsors in the Controller

In this chapter, you will give super admins the ability to add sponsors.  Regular admins, users, and the general public will NOT have this ability.

### New Branch
Enter the command "git checkout -b 03-06-sponsor_create_controller".

### Controller Test
* Edit the file test/controllers/sponsors_controller_test.rb.  Add the following code immediately after the line "class SponsorsControllerTest < ActionDispatch::IntegrationTest":
```
  def create_sponsor
    post sponsors_path, params: { sponsor: { name: 'Pear Computer' } }
  end

  def create_sponsor_disabled
    assert_no_difference 'Sponsor.count' do
      create_sponsor
    end
    assert_redirected_to root_path
  end
```
* Edit the file test/controllers/sponsors_controller_test.rb.  Add the following code just before the last "end" statement:
```
  test 'should redirect create when not logged in' do
    create_sponsor_disabled
  end

  test 'should redirect create when logged in as a user' do
    sign_in @u1, scope: :user
    create_sponsor_disabled
  end

  test 'should redirect create when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    create_sponsor_disabled
  end

  test 'should not redirect create when logged in as a super admin' do
    sign_in @a1, scope: :admin
    assert_difference 'Sponsor.count', 1 do
      create_sponsor
    end
  end
```
* Enter the command "sh testc.sh".  All 4 new controller tests fail because the routing is missing.

### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :sponsors" with the following:
```
  resources :sponsors, only: [:show, :index, :create, :new]
```
* Enter the command "sh testc.sh".  All 4 controller tests fail because the create action is missing from the sponsor controller.

### Sponsor Controller
* Edit the file app/controllers/sponsors_controller.rb.  Immediately after the line "class SponsorsController < ApplicationController", add the following code:
```
  before_action :may_control_sponsor, only: [:create]

```
* Just before the last end statement in app/controllers/sponsors_controller.rb, add the following code:
```
  private

  # BEGIN: private section
  def may_control_sponsor
    return redirect_to(root_path) if no_control == true
  end
  helper_method :may_control_sponsor

  def no_control
    ca = current_admin
    # No control over sponsor given any of the following conditions:
    # 1.  current_admin is nil OR
    # 2.  Not a super admin OR
    ca.nil? || ca.super != true
  end
  helper_method :no_control

  def sponsor_params
    params.require(:sponsor).permit(:name, :phone, :description,
                                    :contact_email, :contact_url, :current)
  end
  # END: private section
```
* Just before the end of the action section in app/controllers/sponsors_controller.rb, add the following code:
```

  def create
    @sponsor = Sponsor.new(sponsor_params)
    if @sponsor.save
      flash[:info] = 'Sponsor added'
      redirect_to sponsors_path
    else
      render 'new'
    end
  end
```
* Enter the command "sh testc.sh".  One test fails, because a missing template stops the process of creating a new sponsor.
* ENter the command "sh git_check.sh".  All tests should now pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor controller create action"
git push origin 03-06-sponsor_create_controller
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh". 
