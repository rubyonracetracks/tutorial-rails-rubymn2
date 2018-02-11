# Unit 4
## Chapter 6: Forhire Edit

### New Branch
Enter the command "git checkout -b 04-06-forhire_edit".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/forhires_controller_test.rb. Add the following code immediately before the end of the definition section:
```
  def edit_forhire
    patch forhire_path(@fh_connery),
          params: { forhire: { description: 'I stopped Largo twice!',
                               email: 'bond1983@rubyonracetracks.com',
                               title: 'James Bond 1983' } }
  end

  def edit_forhire_disabled
    edit_forhire
    assert_redirected_to root_path
  end
```
* Edit the file test/controllers/forhires_controller_test.rb.  Add the following code just before the last "end" statement:
```
  test 'should redirect edit to root when not logged in' do
    edit_forhire_disabled
  end

  # rubocop:disable Metrics/LineLength
  test 'should not redirect edit to root when logged in as the user with the forhire' do
    sign_in @u1, scope: :user
    edit_forhire
    assert_redirected_to forhire_path(@fh_connery)
  end
  # rubocop:enable Metrics/LineLength

  test 'should redirect edit to root when logged in as a different user' do
    sign_in @u2, scope: :user
    edit_forhire_disabled
  end

  test 'should redirect edit to root when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    edit_forhire_disabled
  end

  test 'should redirect edit to root when logged in as a super admin' do
    sign_in @a1, scope: :admin
    edit_forhire_disabled
  end
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because of a missing route.

#### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :forhires" with the following:
```
  resources :forhires, only: [:show, :index, :create, :new, :update, :edit] do
```
* Enter the command "sh testc.sh".  The same 5 tests fail because the update action is not provided in the forhire controller.

#### Forhire Controller
* Just before the end of the before_action section in app/controllers/forhires_controller.rb, add the following code:
```
  before_action :may_edit_forhire, only: [:update]
```
* Just before the end of the action section in app/controllers/forhires_controller.rb, add the following code:
```
  def update
    @forhire = Forhire.find(params[:id])
    if @forhire.update_attributes(forhire_params)
      flash[:success] = 'Forhire updated'
      redirect_to forhire_path(@forhire)
    else
      render 'edit'
    end
  end

  def edit
    @forhire = Forhire.find(params[:id])
  end
```
* Just before the line "def forhire_params" in app/controllers/forhires_controller.rb, add the following code:
```
  def may_edit_forhire
    return redirect_to root_url unless user_signed_in?
    @forhire = current_user.forhires.find_by(id: params[:id])
    return redirect_to root_url if @forhire.nil?
  end
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added forhire edit (controller level)"
```

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test forhire_edit".
* In the file test/integration/forhire_edit_test.rb, replace everything between the line "class ForhireEditTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def check_no_edit_button
    visit forhire_path(@fh_connery)
    assert page.has_no_link?('Edit For Hire Profile', href: forhire_path(@fh_connery))
  end

  test 'visitor may not edit forhire' do
    check_no_edit_button
  end

  test 'wrong user may not edit forhire' do
    login_as(@u2, scope: :user)
    check_no_edit_button
  end

  test 'correct user gets button to edit forhire' do
    login_as(@u1, scope: :user)
    visit forhire_path(@fh_connery)
    assert page.has_link?('Edit For Hire Profile', href: edit_forhire_path(@fh_connery))
  end

  test 'regular admin may not edit forhire' do
    login_as(@a4, scope: :admin)
    check_no_edit_button
  end

  test 'super admin may not edit forhire' do
    login_as(@a1, scope: :admin)
    check_no_edit_button
  end

  test 'correct user can successfully edit forhire' do
    login_as(@u1, scope: :user)
    visit edit_forhire_path(@fh_connery)

    assert page.has_css?('title', text: full_title('Edit For Hire Profile'),
                                  visible: false)

    fill_in('Title', with: 'James Bond 1983')
    fill_in('Email', with: 'nsna@rubyonracetracks.com')
    fill_in('Background Statement', with: 'I stopped Largo twice!')

    click_button('Submit')
    assert page.has_css?('title',
                         text: full_title('Sean Connery: James Bond 1983'),
                         visible: false)
    assert page.has_css?('h1', text: 'For Hire')
    assert_text 'James Bond 1983'
    assert_text 'nsna@rubyonracetracks.com'
    assert_text 'I stopped Largo twice!'
  end
```
  * Enter the command "sh test_app.sh".  Two of the new integration tests fail.
  * Enter the command "alias test1='command for running failed tests minus the TESTOPTS portion'".
  * Enter the command "test1".  The same two integration tests fail.  One test fails because the correct user does not get the expected edit button.  The other test fails because the forhire edit page is missing.

#### Edit Forhire Button
* Edit the file app/views/forhires/show.html.erb. Immediately after the h1 heading near the top of the page, add the following code:
```
<% # BEGIN: edit forhire button %>
<% if user_signed_in? && current_user == @user %>
  <%= link_to "Edit For Hire Profile", edit_forhire_path(@forhire),
              class: "btn btn-lg btn-primary"
    %>
  <br><br>
<% end %>
<% # END: edit forhire button %>
```
* Enter the command "test1".  Now only one test fails, and it's because the forhire edit page is still missing.

#### Edit Forhire Form
* Create the file app/views/forhires/edit.html.erb.  Give it the following content:
```
<% provide(:title, 'Edit For Hire Profile') %>

<h1>Edit For Hire Profile</h1>

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
        <%= f.label :description, 'Background Statement' %><br />
        <%= f.text_area :description, rows: 15 %>
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
git commit -m "Added forhire edit (view level)"
git push origin 04-06-forhire_edit
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
