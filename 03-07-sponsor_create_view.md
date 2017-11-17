# Unit 3
## Chapter 7: Form For Creating Sponsors

In this chapter, you will provide the pages needed to add new sponsors.

### New Branch
Enter the command "git checkout -b 03-07-sponsor_create_view".

### Integration Test
* Enter the command "rails generate integration_test sponsor_create".
* In the file test/integration/sponsor_create_test.rb, replace everything between the line "class SponsorCreateTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def check_no_create_button
    visit sponsors_path
    assert page.has_no_link?('Add Sponsor', href: sponsors_path)
  end

  test 'visitor does not get button to add sponsor' do
    check_no_create_button
  end

  test 'user does not get button to add sponsor' do
    login_as(@u1, scope: :user)
    check_no_create_button
  end

  test 'regular admin does not get button to add sponsor' do
    login_as(@a4, scope: :admin)
    check_no_create_button
  end

  test 'super admin gets button to add sponsor' do
    login_as(@a1, scope: :admin)
    visit sponsors_path
    assert page.has_link?('Add Sponsor', href: new_sponsor_path)
  end

  # rubocop:disable Metrics/BlockLength
  test 'super admin can successfully add sponsors' do
    login_as(@a1, scope: :admin)
    visit sponsors_path

    # Add current sponsor
    click_on 'Add Sponsor'
    assert page.has_css?('title', text: full_title('Add Sponsor'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Add Sponsor')
    fill_in('Name', with: 'Richmond & Woods Law Offices')
    fill_in('Phone', with: '202-555-0111')
    fill_in('Description', with: 'Sassy and smart!')
    fill_in('Email', with: 'info@richmondwoods.com')
    fill_in('URL', with: 'http://www.richmondwoods.com')
    check('Current')
    click_button('Submit')
    click_on 'Richmond & Woods Law Offices'
    assert page.has_css?('h1', text: 'Current Sponsor: Richmond & Woods Law Offices')
    click_on 'Home'
    assert page.has_text?('Richmond & Woods Law Offices')

    # Add past sponsor
    click_on 'Sponsors'
    click_on 'Add Sponsor'
    assert page.has_css?('title', text: full_title('Add Sponsor'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Add Sponsor')
    fill_in('Name', with: 'Scrooge & Marley')
    fill_in('Phone', with: '020 7946 0123')
    fill_in('Description', with: 'Greedy misers!')
    fill_in('Email', with: 'ebenezer@scroogeandmarley.com')
    fill_in('URL', with: 'http://www.scroogeandmarley.com')
    click_button('Submit')
    click_on 'Scrooge & Marley'
    assert page.has_css?('h1', text: 'Past Sponsor: Scrooge & Marley')
    click_on 'Home'
    assert_not page.has_text?('Scrooge & Marley')
  end
  # rubocop:enable Metrics/BlockLength
```
* Enter the command "sh test_app.sh".  The last two tests fail.
* Enter the command "alias test1='(command to run failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same two tests fail.

### Sponsor Index Page
* Edit the file app/views/sponsors/index.html.erb.  Immediately after the h1 heading near the top of the page, add the following code:
```
<% # BEGIN: add sponsor button %>
<% if admin_signed_in? && current_admin.super %>
  <%= link_to "Add Sponsor", new_sponsor_path,
              class: "btn btn-lg btn-primary"
    %>
<% end %>
<% # END: add sponsor button %>
```
* Enter the command "test1".  Now only one test fails.  The reason is a missing template.

### Add Sponsor Page
* Add the file app/views/sponsors/new.html.erb and give it the following content:
```
<% provide(:title, 'Add Sponsor') %>

<h1>Add Sponsor</h1>

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
* Enter the command "test1".  The test fails because the shared error template is missing.

### Add Error Messages
* Enter the following commands:
```
mkdir app/views/shared
touch app/views/shared/_error_messages.html.erb
```
* Add the file app/views/shared/_error_messages.html.erb and give it the following content:
```
<% if object.errors.any? %>
  <div id="error_explanation">
    <div class="alert alert-danger">
      The form contains <%= pluralize(object.errors.count, "error") %>.
    </div>
    <ul>
    <% object.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
    </ul>
  </div>
<% end %>
```

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor addition form"
git push origin 03-07-sponsor_create_view
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh". 
