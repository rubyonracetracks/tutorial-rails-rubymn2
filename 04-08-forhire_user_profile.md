# Unit 4
## Chapter 8: Adding Forhire Display to User Profile

In this chapter, you will add the for hire profile to the associated user's profile page.  You will also give users the ability to add a for hire profile (if they don't already have one) from their own profile pages.

### New Branch
Enter the command "git checkout -b 04-08-user_forhire".

### Integration Test
* Enter the command "rails generate integration_test forhire_user_profile".
* Edit the file test/integration/forhire_user_profile_test.rb.  Replace everything between the line "class ForhireUserProfileTest < ActionDispatch::IntegrationTest" and the last end statement with the following:
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


### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added forhire display and create button to user profile page"
git push origin 04-08-user_forhire
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
