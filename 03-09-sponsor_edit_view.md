# Unit 3
## Chapter 9: Editing Sponsors
In this chapter, you will provide a form that allows super admins to edit sponsor information.  Again, only super admins may do this.

### New Branch
Enter the command "git checkout -b 03-09-sponsor_edit_view".

### Integration Test
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

### Edit Sponsor Button
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

### Edit Sponsor Form
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
git commit -m "Added the sponsor edit form"
git push origin 03-09-sponsor_edit_view
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh". 
