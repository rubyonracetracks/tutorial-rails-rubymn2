# Unit 5
## Chapter 5: Opening Create

In this chapter, you will give users the ability to add a job opening.

### New Branch
Enter the command "git checkout -b 06-05-opening_create".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/openings_controller_test.rb.  Add the following code immediately just before the end of the definitions section:
```
  def create_opening
    title1 = 'Saboteur'
    desc1 = 'Sabotage the helicopter to be used to pick up James Bond'
    post openings_path, params: { opening: { title: title1,
                                             description: desc1 } }
  end

  def create_opening_disabled
    assert_no_difference 'Opening.count' do
      create_opening
    end
    assert_redirected_to root_path
  end
```
* Edit the file test/controllers/openings_controller_test.rb.  Add the following code immediately before the last "end" statement:
```
  test 'should redirect create when not logged in' do
    create_opening_disabled
  end

  test 'should not redirect user' do
    sign_in @u7, scope: :user
    assert_difference 'Opening.count', 1 do
      create_opening
    end
    assert_redirected_to user_path(@u7)
  end

  test 'should redirect create for regular admin' do
    sign_in @a4, scope: :admin
    create_opening_disabled
  end

  test 'should redirect create for super admin' do
    sign_in @a1, scope: :admin
    create_opening_disabled
  end
```
* Enter the command "sh testc.sh".  All 4 new tests fail because the expected routing is not provided.

#### Routing
* Edit the file config/routes.rb.  Replace the line that begins with "resources :openings" with the following:
```
  resources :openings, only: [:show, :index, :create, :new] do
```
* Enter the command "sh testc.sh".  The 4 tests fail because the create action could not be found..

#### Controller
* Edit the file app/controllers/openings_controller.rb.  Just before the end of the before_action section, add the following code:
```
  before_action :may_create_opening, only: [:create]
```
* Edit the file app/controllers/openings_controller.rb.  Immediately before the end of the private section, add the following code:
```
  def may_create_opening
    return redirect_to(root_path) unless user_signed_in?
  end
  helper_method :may_create_opening

  def opening_params
    params.require(:opening).permit(:title, :source_code_url,
                                    :deployed_url, :description)
  end
```
* Edit the file app/controllers/openings_controller.rb.  Add the following code just before the end of the action section:
```
  def create
    @opening = current_user.openings.build(opening_params)
    if @opening.save
      flash[:info] = 'Job opening added'
      redirect_to user_path(current_user)
    else
      render 'new'
    end
  end

  def new
    @opening = Opening.new
  end
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the opening create capability (controller level)"
```

### Part B: View Level

Please note that a user who successfully adds a job opening is redirected to his/her profile page.  In a later chapter, you will add the opening and the "Add Job Opening" button on the user profile page.  In this chapter, the focus is on providing the working "Add Job Opening" button on the opening index page.

#### Integration Test
* Enter the command "rails generate integration_test opening_create".
* Edit the file test/integration/opening_create_test.rb.  Replace everything between the line "class OpeningCreateTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def check_no_create_button
    visit openings_path
    assert page.has_no_link?('Add Job Opening', href: openings_path)
  end

  test 'user gets button to add opening' do
    login_as(@u7, scope: :user)
    visit openings_path
    assert page.has_link?('Add Job Opening', href: new_opening_path)
  end

  test 'regular admin does not get button to add opening' do
    login_as(@a4, scope: :admin)
    check_no_create_button
  end

  test 'super admin does not get button to add opening' do
    login_as(@a1, scope: :admin)
    check_no_create_button
  end

  test 'user can successfully add opening' do
    login_as(@u7, scope: :user)
    visit openings_path

    click_on 'Add Job Opening'
    assert page.has_css?('title', text: full_title('Add Job Opening'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Add Job Opening')
    fill_in('Title', with: 'Death Trap Builder')
    fill_in('Description', with: 'Build death traps that will kill 007!')
    click_button('Submit')

    assert page.has_css?('title', text: full_title('User: Ernst Blofeld'),
                                  visible: false)
    assert page.has_css?('h1', text: 'User: Ernst Blofeld')

    click_on 'Home'
    click_on 'Job Openings'
    assert page.has_css?('title', text: full_title('Job Opening Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Job Opening Index')
    assert_text 'Death Trap Builder'
    assert_text 'Build death traps that will kill 007!'
  end
```
* Enter the command "sh test_app.sh".  Two of the new integration tests fail.
* Enter the command "alias test1='(command to run the failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same two integration tests fail.  Both tests fail because the link to add a new opening is missing.

#### Opening Index
* Edit the file app/views/openings/index.html.erb. Immediately after the listing of number of openings, add the following code:
```
<% # BEGIN: add opening button %>
<% if user_signed_in? %>
  <%= link_to "Add Job Opening", new_opening_path,
              class: "btn btn-lg btn-primary"
    %>
    <br>
<% end %>
<% # END: add opening button %>
```
* Enter the command "test1".  One test fails because the form for entering a opening is absent.

#### Form For Adding Opening
* Enter the command "touch app/views/openings/new.html.erb".  
* Enter the command "test1".  Now the test fails because the page for entering parameters for the opening is blank.
* Give the blank file app/views/openings/new.html.erb the following content:
```
<% provide(:title, 'Add Job Opening') %>

<h1>Add Job Opening</h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(@opening) do |f| %>
      <%= render 'shared/error_messages', object: f.object %>

      <div class="field">
        <%= f.label :title %><br />
        <%= f.text_field :title %>
      </div>

      <div class="field">
        <%= f.label :description %><br />
        <%= f.text_area :description, placeholder: "Enter background statement here...", rows: 15 %>
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
git commit -m "Added the opening create capability (view level)"
git push origin 06-05-opening_create
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh". 
