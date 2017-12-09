# Unit 4
## Chapter 5: Forhire Create

In this chapter, you will give users the ability to create a forhire profile.  The create method will be limited to users who do not already have one.  As explained in an earlier chapter, my attempts to use has_one did not pan out.

### New Branch
Enter the command "git checkout -b 04-05-forhire_create".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/forhires_controller_test.rb.  Add the following code immediately after the line "class ForhiresControllerTest < ActionDispatch::IntegrationTest":
```
  def create_forhire
    title1 = 'Master Villain'
    email1 = 'ernst_stavro_blofeld@example.com'
    blurb1 = 'I am out to take over the world!'
    post forhires_path, params: { forhire: { title: title1,
                                             email: email1,
                                             blurb: blurb1 } }
  end

  def create_forhire_disabled
    assert_no_difference 'Forhire.count' do
      create_forhire
    end
    assert_redirected_to root_path
  end
```
* Edit the file test/controllers/forhires_controller_test.rb.  Add the following code immediately before the last "end" statement:
```
  test 'should redirect create when not logged in' do
    create_forhire_disabled
  end

  test 'should redirect create for user who already has a forhire profile' do
    sign_in @u1, scope: :user
    create_forhire_disabled
  end

  test 'should not redirect user without an existing forhire profile' do
    sign_in @u7, scope: :user
    assert_difference 'Forhire.count', 1 do
      create_forhire
    end
  end

  test 'should redirect create for regular admin' do
    sign_in @a4, scope: :admin
    create_forhire_disabled
  end

  test 'should redirect create for super admin' do
    sign_in @a1, scope: :admin
    create_forhire_disabled
  end
```
* Enter the command "sh testc.sh".  All 5 new tests fail because the create action is not provided by the forhire controller.

#### Controller
* Edit the file app/controllers/forhires_controller.rb.  Immediately after the line "class ForhiresController < ApplicationController", add the following code:
```
  # BEGIN: before_action section
  before_action :may_create_forhire, only: [:create]
  # END: before_action section
```
* Edit the file app/controllers/forhires_controller.rb.  Immediately before the last "end" statement, add the following code:
```
  # BEGIN: private section
  # rubocop:disable Metrics/LineLength
  def may_create_forhire
    return redirect_to(root_path) unless user_signed_in?
    return redirect_to(root_path) unless Forhire.where("user_id=#{current_user.id}").empty?
  end
  helper_method :may_create_forhire
  # rubocop:enable Metrics/LineLength

  def forhire_params
    params.require(:forhire).permit(:title, :email, :blurb)
  end
  # END: private section
```
* Edit the file app/controllers/forhires_controller.rb.  Add the following code just before the end of the action section:
```
  def create
    @forhire = current_user.forhires.build(forhire_params)
    if @forhire.save
      flash[:info] = 'For hire profile added'
      redirect_to forhires_path
    else
      render 'new'
    end
  end

  def new
    @sponsor = Forhire.new
  end
```
* Enter the command "sh testc.sh".  All tests now pass.

#### Template
* Enter the command "touch app/views/forhires/new.html.erb".

### Part B: View Level

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the ability to add forhires at the view level"
git push origin 04-05-forhire_create
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh". 
