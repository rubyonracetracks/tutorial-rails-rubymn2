# Unit 6
## Chapter 6: Opening Edit

### New Branch
Enter the command "git checkout -b 06-06-opening_edit".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/openings_controller_test.rb.  Just before the line "class OpeningsControllerTest < ActionDispatch::IntegrationTest", add the following line:
```
# rubocop:disable Metrics/ClassLength
```
* Edit the file test/controllers/openings_controller_test.rb.  Just after the last "end" statement, add the following line:
```
# rubocop:enable Metrics/ClassLength
```
* Edit the file test/controllers/openings_controller_test.rb. Add the following code immediately before the end of the definition section:
```
  def edit_opening
    title1 = 'Deputy of Texarkana'
    desc1 = 'Apprehend those punks while I am busy chasing the Bandit!'
    patch opening_path(@op4),
          params: { opening: { title: title1,
                               description: desc1 } }
  end

  def edit_opening_disabled
    edit_opening
    assert_redirected_to root_path
  end
```
* Edit the file test/controllers/openings_controller_test.rb.  Add the following code just before the last "end" statement:
```
  test 'should redirect edit to root when not logged in' do
    edit_opening_disabled
  end

  # rubocop:disable Metrics/LineLength
  test 'should not redirect edit to root when logged in as the user with the opening' do
    sign_in @u11, scope: :user
    edit_opening
    assert_redirected_to opening_path(@op4)
  end
  # rubocop:enable Metrics/LineLength

  test 'should redirect edit to root when logged in as a different user' do
    sign_in @u2, scope: :user
    edit_opening_disabled
  end

  test 'should redirect edit to root when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    edit_opening_disabled
  end

  test 'should redirect edit to root when logged in as a super admin' do
    sign_in @a1, scope: :admin
    edit_opening_disabled
  end
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because of a missing route.

#### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :openings" with the following:
```
  resources :openings, only: [:show, :index, :create, :new, :update, :edit] do
```
* Enter the command "sh testc.sh".  The same 5 tests fail because the update action is not provided in the opening controller.

#### Opening Controller
* Just before the end of the before_action section in app/controllers/openings_controller.rb, add the following code:
```
  before_action :may_edit_opening, only: [:update]
```
* Just before the end of the action section in app/controllers/openings_controller.rb, add the following code:
```
  def update
    @opening = Opening.find(params[:id])
    if @opening.update_attributes(opening_params)
      flash[:success] = 'Job opening updated'
      redirect_to opening_path(@opening)
    else
      render 'edit'
    end
  end

  def edit
    @opening = Opening.find(params[:id])
  end
```
* Just before the line "def opening_params" in app/controllers/openings_controller.rb, add the following code:
```
  def may_edit_opening
    return redirect_to root_url unless user_signed_in?
    @opening = current_user.openings.find_by(id: params[:id])
    return redirect_to root_url if @opening.nil?
  end
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added opening edit (controller level)"
```

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test opening_edit".
* In the file test/integration/opening_edit_test.rb, replace everything between the line "class OpeningEditTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def check_no_edit_button
    visit opening_path(@op4)
    assert page.has_no_link?('Edit Job Opening', href: opening_path(@op4))
  end

  test 'visitor may not edit opening' do
    check_no_edit_button
  end

  test 'wrong user may not edit opening' do
    login_as(@u2, scope: :user)
    check_no_edit_button
  end

  test 'correct user gets button to edit opening' do
    login_as(@u3, scope: :user)
    visit opening_path(@op4)
    assert page.has_link?('Edit Job Opening', href: edit_opening_path(@op4))
  end

  test 'regular admin may not edit opening' do
    login_as(@a4, scope: :admin)
    check_no_edit_button
  end

  test 'super admin may not edit opening' do
    login_as(@a1, scope: :admin)
    check_no_edit_button
  end

  test 'correct user can successfully edit opening' do
    login_as(@u3, scope: :user)
    visit edit_opening_path(@op4)

    assert page.has_css?('title', text: full_title('Edit Job Opening'),
                                  visible: false)
    fill_in('Title', with: 'Lawyer')
    fill_in('Description', with: 'Defend me from lawsuits')
    click_button('Submit')

    assert page.has_css?('title',
                         text: full_title('Job Opening: Lawyer'),
                         visible: false)
    assert page.has_css?('h1', text: 'Job Opening: Lawyer')
    assert_text 'Defend me from lawsuits'
  end
```
  * Enter the command "sh test_app.sh".  Two of the new integration tests fail.
  * Enter the command "alias test1='command for running failed tests minus the TESTOPTS portion'".
  * Enter the command "test1".  The same two integration tests fail.  One test fails because the correct user does not get the expected edit button.  The other test fails because of a missing template.

#### Edit Job Opening Button  
* Edit the file app/views/openings/show.html.erb. Immediately after the h1 heading near the top of the page, add the following code:
```
<% # BEGIN: edit opening button %>
<% if user_signed_in? && current_user == @user %>
  <%= link_to "Edit Job Opening", edit_opening_path(@opening),
              class: "btn btn-lg btn-primary"
    %>
  <br><br>
<% end %>
<% # END: edit opening button %>
```
* Enter the command "test1".  Now only one test fails, and it's because of a missing template.

#### Edit Opening Form
* Enter the command "touch app/views/openings/edit.html.erb".
* Enter the command "test1".  Now the test fails because the opening edit page does not have the expected content.
* Give the file app/views/openings/edit.html.erb the following content:
```
<% provide(:title, 'Edit Job Opening') %>

<h1>Edit Job Opening</h1>

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
        <%= f.text_area :description, placeholder: "Enter description here...", rows: 15 %>
      </div>

      <%= f.submit "Submit", class: "btn btn-primary" %>
    <% end %>
  </div>
</div>
```
* Enter the command "test1".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should now pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added opening edit (view level)"
git push origin 06-06-opening_edit
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
