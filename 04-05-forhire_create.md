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
    @forhire = Forhire.new
  end
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the forehire create capability (controller level)"
```

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test forhire_create".
* Edit the file test/integration/forhire_create_test.rb.  Replace everything between the line "class ForhireCreateTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def check_no_create_button
    visit forhires_path
    assert page.has_no_link?('Add For Hire Profile', href: forhires_path)
  end

  test 'visitor does not get button to add forhire' do
    check_no_create_button
  end

  test 'user with forhire profile does not get button to add forhire' do
    login_as(@u1, scope: :user)
    check_no_create_button
  end

  test 'user without forhire profile gets button to add forhire' do
    login_as(@u7, scope: :user)
    visit forhires_path
    assert page.has_link?('Add For Hire Profile', href: new_forhire_path)
  end

  test 'regular admin does not get button to add forhire' do
    login_as(@a4, scope: :admin)
    check_no_create_button
  end

  test 'super admin does not get button to add forhire' do
    login_as(@a1, scope: :admin)
    check_no_create_button
  end

  test 'user without forhire profile can successfully add forhire' do
    login_as(@u7, scope: :user)
    visit forhires_path

    click_on 'Add For Hire Profile'
    assert page.has_css?('title', text: full_title('Add Your For Hire Profile'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Add Your For Hire Profile')
    fill_in('Title', with: 'Master Villain')
    fill_in('Email', with: 'ernst_stavro_blofeld@example.com')
    fill_in('Background Statement', with: 'I am out to take over the world!')
    click_button('Submit')

    assert page.has_css?('title', text: full_title('For Hire Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'For Hire Index')
    assert_text 'ernst_stavro_blofeld@example.com'
    assert_text 'I am out to take over the world!'
    click_on 'Master Villain'

    assert page.has_css?('title',
                         text: full_title('Ernst Blofeld: Master Villain'),
                         visible: false)
    assert page.has_css?('h1', text: 'For Hire')
    assert_text 'Master Villain'
    assert_text 'ernst_stavro_blofeld@example.com'
    assert_text 'I am out to take over the world!'
  end
```
* Enter the command "sh test_app.sh".  Two of the new integration tests fail.
* Enter the command "alias test1='(command to run the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same two integration tests fail.  Both tests fail because the link to add a new for hire profile is missing.

#### For Hire Index
* Edit the file app/views/forhires/index.html.erb. Immediately after the h1 heading near the top of the page, add the following code:
```
<% # BEGIN: add forhire button %>
<% if user_signed_in? && current_user.forhires.count < 1 %>
  <%= link_to "Add For Hire Profile", new_forhire_path,
              class: "btn btn-lg btn-primary"
    %>
<% end %>
<% # END: add forhire button %>
```
* Enter the command "test1".  Two tests fail.  One test fails because the Forhire object with the id of "new" could not be found.  This is because new_forhire_path has not been defined.

#### Routing
* Edit the file config/routes.rb.  Replace the line that begins with "resources :forhires, only:" with the following:
```
  resources :forhires, only: [:show, :index, :create, :new]
```
* Enter the command "test1".  The test fails because of a missing template.

#### Form For Adding Forhire Profile
* Enter the command "touch app/views/forhires/new.html.erb".  
* Enter the command "test1".  Now the test fails because the page for entering parameters for the for hire profile is blank.
* Give the blank file app/views/forhires/new.html.erb the following content:
```
<% provide(:title, 'Add Your For Hire Profile') %>

<h1>Add Your For Hire Profile</h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(@forhire) do |f| %>
      <%= render 'shared/error_messages', object: f.object %>

      <div class="field">
        <%= f.label :title %><br />
        <%= f.text_field :title %>
      </div>

      <div class="field">
        <%= f.label :email, 'Email' %><br />
        <%= f.text_field :email %>
      </div>

      <div class="field">
        <%= f.label :blurb, 'Background Statement' %><br />
        <%= f.text_area :blurb, placeholder: "Enter background statement here...", rows: 15 %>
      </div>

      <%= f.submit "Submit", class: "btn btn-primary" %>
    <% end %>
  </div>
</div>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

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
