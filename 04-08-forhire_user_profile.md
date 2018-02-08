# Unit 4
## Chapter 8: Adding Forhire Profile to User Profile

```
  def check_user_pages
    visit user_path(@u1)
    assert page.has_link?('James Bond 1962-1971', href: forhire_path(@fh_connery))
    visit user_path(@u2)
    assert page.has_link?('James Bond 1969', href: forhire_path(@fh_lazenby))
  end

  test 'user sees the expected content on pages' do
    login_as(@u1, scope: :user)
    check_forhire_pages
    check_user_pages
  end

  test 'regular admin sees the expected content on pages' do
    login_as(@a4, scope: :admin)
    check_forhire_pages
    check_user_pages
  end

  test 'super admin sees the expected content on pages' do
    login_as(@a1, scope: :admin)
    check_forhire_pages
    check_user_pages
  end
```

#### User Profile Page
* Edit the file app/controllers/users_controller.rb.  At the end of the "def show" definition, add the following line:
```
    @forhire = Forhire.where("user_id=#{@user.id}").first
```
* Enter the command "sh testc.sh".  (This step is necessary to make sure that the above change doesn't break the user controller.)  All tests should pass.
* Edit the file app/views/users/show.html.erb.  Add the following code after the delete button section:
```
    <% # BEGIN: forhire section %>
    <% if Forhire.where("user_id=#{@user.id}").any? %>
      <h3>Profile</h3>
      Title: <%= link_to @forhire.title, forhire_path(@forhire) %>
      <br>
      <%= @forhire.description[0..140] %>
      <br>
    <% end %>
    <% # END: forhire section %>
```
* Enter the command "test1".  All test should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
