# Unit 3
## Chapter 7: Editing Sponsors in the Controller
In this chapter, you will give super admins the ability to edit sponsor information at the controller level.  Again, only super admins may do this.

### New Branch
Enter the command "git checkout -b 03-05-sponsor_edit".

### Part A: Controller Level

#### Controller Test
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
    assert_equal @sponsor1.description, 'parody of Best Buy'
  end
```
* Enter the command "sh testc.sh".  The last test fails because of a missing route.

#### Routing
* Edit the file config/routes.rb.  Replace the line containing "resources :sponsors" with the following:
```
  resources :sponsors, only: [:show, :index, :create, :new, :update, :edit]
```
* Enter the command "sh testc.sh".  The test fails because the update action is not provided in the sponsor controller.

#### Sponsor Controller
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
      redirect_to sponsor_path(@sponsor)
    else
      render 'edit'
    end
  end

  def edit
    @sponsor = Sponsor.find(params[:id])
  end
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should now pass, and there should be no offenses.

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test sponsor_edit".
* In the file test/integration/sponsor_edit_test.rb, replace everything between the line "class SponsorEditTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def check_no_edit_button
    visit sponsor_path(@sponsor1)
    assert page.has_no_link?('Edit Sponsor', href: sponsor_path(@sponsor1))
  end

  test 'visitor does not get button to edit sponsor' do
    check_no_edit_button
  end

  test 'user does not get button to add sponsor' do
    login_as(@u1, scope: :user)
    check_no_edit_button
  end

  test 'regular admin does not get button to add sponsor' do
    login_as(@a4, scope: :admin)
    check_no_edit_button
  end

  test 'super admin gets button to add sponsor' do
    login_as(@a1, scope: :admin)
    visit sponsor_path(@sponsor1)
    assert page.has_link?('Edit Sponsor', href: sponsor_path(@sponsor1))
  end

  # rubocop:disable Metrics/BlockLength
  test 'super admin can successfully edit sponsors' do
    login_as(@a1, scope: :admin)
    visit sponsor_path(@sponsor1)

    # Edit current sponsor
    click_on 'Edit Sponsor'
    assert page.has_css?('title', text: full_title('Edit Sponsor'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Edit Sponsor')
    fill_in('Name', with: 'King Kamehameha Club')
    fill_in('Phone', with: '808-555-0111')
    fill_in('Description', with: 'The beach club in _Magnum P.I._!')
    fill_in('Email', with: 'rick_wright@kingkamehamehaclub.com')
    fill_in('URL', with: 'http://www.kingkamehamehaclub.com')
    uncheck('Current')
    click_button('Submit')
    click_on 'King Kamehameha Club'
    assert page.has_css?('h1', text: 'Past Sponsor: King Kamehameha Club')
    assert page.has_text?('808-555-0111')
    assert page.has_text?('The beach club in _Magnum P.I._!')
    assert page.has_text?('rick_wright@kingkamehamehaclub.com')
    assert page.has_text?('http://www.kingkamehamehaclub.com')

    # Edit past sponsor
    visit sponsor_path(@sponsor3)
    click_on 'Edit Sponsor'
    assert page.has_css?('title', text: full_title('Edit Sponsor'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Edit Sponsor')
    fill_in('Name', with: "Coop's Beer")
    fill_in('Phone', with: '888-555-0111')
    fill_in('Description', with: "parody of Coor's Beer")
    fill_in('Email', with: 'info@coopsbeer.com')
    fill_in('URL', with: 'http://www.coopsbeer.com')
    check('Current')
    click_button('Submit')
    click_on "Coop's Beer"
    assert page.has_css?('h1', text: "Current Sponsor: Coop's Beer")
    assert page.has_text?('888-555-0111')
    assert page.has_text?("parody of Coor's Beer")
    assert page.has_text?('info@coopsbeer.com')
    assert page.has_text?('http://www.coopsbeer.com')
  end
  # rubocop:enable Metrics/BlockLength
  ```
  * Enter the command "sh test_app.sh".  The last two new integration tests fail.
  * Enter the command "alias test1='command for running failed tests minus the TESTOPTS portion'".
  * Enter the command "test1".  The same two integration tests fail.

#### Edit Sponsor Button
* Edit the file app/views/sponsors/show.html.erb.  After the h1 header section, add the following code:
```
<% # BEGIN: edit sponsor button %>
<% if admin_signed_in? && current_admin.super %>
  <%= link_to "Edit Sponsor", edit_sponsor_path(@sponsor),
              class: "btn btn-lg btn-primary"
    %>
<% end %>
<% # END: edit sponsor button %>
```
* Enter the command "test1".  Now only one integration test fails.  It's time to provide a page for editing the sponsor.

#### Edit Sponsor Form
* Create the file app/views/sponsors/edit.html.erb.  Give it the following content:
```
<% provide(:title, 'Edit Sponsor') %>

<h1>Edit Sponsor</h1>

<div class="row">
  <div class="col-md-6 col-md-offset-3">
    <%= form_for(@sponsor) do |f| %>
      <%= render 'shared/error_messages', object: f.object %>

      <div class="field">
        <%= f.label :name %><br />
        <%= f.text_field :name %>
      </div>

      <div class="field">
        <%= f.label :phone %><br />
        <%= f.text_field :phone %>
      </div>

      <div class="field">
        <%= f.label :contact_email, 'Sponsor Email' %><br />
        <%= f.text_field :contact_email %>
      </div>

      <div class="field">
        <%= f.label :contact_url, 'Sponsor URL' %><br />
        <%= f.text_field :contact_url %>
      </div>

      <div class="field">
        <%= f.label :description %><br />
        <%= f.text_area :description, placeholder: "Describe the sponsor here...", rows: 15 %>
      </div>

      <div class="field">
        <%= f.label :current, class: "checkbox inline" do %>
          <%= f.check_box :current %>
          <br>
          <span>Current sponsor? (check the above box)</span>
        <% end %>
      </div>

      <%= f.submit "Submit", class: "btn btn-primary" %>
    <% end %>
  </div>
</div>
```

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the ability to edit sponsors"
git push origin 03-05-sponsor_edit
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh". 
