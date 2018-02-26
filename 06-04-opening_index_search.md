# Unit 6
## Chapter 4: Opening Index Search

In this chapter, you will add a search engine to the opening index page.  Because unregistered visitors who try to view job openings are automatically redirected to the user login page, they are not relevant in this chapter.

### New Branch
Enter the command "git checkout -b 06-04-opening_index_search".

### Integration Test
* Enter the command "rails generate integration_test opening_search".
* Edit the file test/integration/opening_search_test.rb.  Replace everything between the line "class OpeningSearchTest < ActionDispatch::IntegrationTest" and the last end statement with the following code:
```
  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  def opening_search_enabled
    add_extra_openings
    visit openings_path
    click_on 'Openings'
    select 'Title', from: 'q[c][0][a][0][name]'
    select 'contains', from: 'q[c][0][p]'
    fill_in('q[c][0][v][0][value]', with: 'Opening')
    click_on 'Search'
    assert page.has_css?('title', text: full_title('Job Opening Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Job Opening Index')
    first(:link, '2').click
    assert page.has_css?('title', text: full_title('Job Opening Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Job Opening Index')
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength

  test 'user can search job openings' do
    login_as(@u1, scope: :user)
    opening_search_enabled
  end

  test 'regular admin can search job openings' do
    login_as(@a4, scope: :admin)
    opening_search_enabled
  end

  test 'super admin can search job openings' do
    login_as(@a1, scope: :admin)
    opening_search_enabled
  end
```
* Enter the command "sh test_app.sh".  The 3 new integration tests will fail.
* Enter the command "alias test1='(Command to run failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The same 3 tests fail because the search engine elements are missing.

### Views
* Create the file app/views/openings/_condition_fields.html.erb and give it the following content:
```
<div class="field">
  <%= f.attribute_fields do |a| %>
    <%= a.attribute_select associations: [:category] %>
  <% end %>
  <%= f.predicate_select %>
  <%= f.value_fields do |v| %>
    <%= v.text_field :value %>
  <% end %>
  <%= link_to "remove", '#', class: "remove_fields" %>
</div>
```
* Add the following code just before the h1 heading in app/views/openings/index.html.erb
```
<br><br>

<%= search_form_for @search, url: search_openings_path, method: :post do |f| %>
  <%= f.condition_fields do |c| %>
    <%= render "condition_fields", f: c %>
  <% end %>
  <p><%= link_to_add_fields "Add Conditions", f, :condition %></p>
  <div class="field">
    Sort:
    <%= f.sort_fields do |s| %>
      <%= s.sort_select %>
    <% end %>
  </div>
  <div class="actions"><%= f.submit "Search" %></div>
<% end %>
```
* Enter the command "test1".  Now your 3 test fail because search_openings_path is undefined.

### Routing
* Edit the config/routes.rb file.  Replace the line that begins with "resources :openings" with the following:
```
  resources :openings, only: [:show, :index] do
    root to: 'openings#index'
    collection { post :search, to: 'openings#index' }
  end
```
* Enter the command "test1". Now your 3 tests fail because no ransack search object is provided.

### Controller
* Edit the file app/controllers/openings_controller.rb and replace the definition of the index action with the following code:
```
  # rubocop:disable Metrics/AbcSize
  def index
    @search = Opening.search(params[:q].presence)
    # NOTE: The following line specifies the sort order.
    # This is reflected in the default sort criteria shown.
    # The user is free to remove these default criteria.
    @search.sorts = 'updated_at desc' if @search.sorts.empty?
    @search.build_condition if @search.conditions.empty?
    @search.build_sort if @search.sorts.empty?
    @openings = @search.result
    @openings_count = @openings.count
    @openings = @openings.order('updated_at desc').page(params[:page]).per(50)
  end
  # rubocop:enable Metrics/AbcSize
```
* Enter the command "test1".  All tests should pass, but you're not quite done yet.

### app/assets/javascripts/openings.coffee
* To allow the "Add conditions" and "remove" buttons to work, add the following code to the end of the file app/assets/javascripts/openings.coffee:
```

jQuery ->
  $('form').on 'click', '.remove_fields', (event) ->
    $(this).closest('.field').remove()
    event.preventDefault()

  $('form').on 'click', '.add_fields', (event) ->
    time = new Date().getTime()
    regexp = new RegExp($(this).data('id'), 'g')
    $(this).before($(this).data('fields').replace(regexp, time))
    event.preventDefault()
```
* Enter the command "test1".  The test should pass.

### Model
* It's time to limit the number of parameters available for searching the opening database.  Edit the file app/models/opening.rb.  Add the following code just before the last "end" statement:
```
  # Limit the parameters available for searching the opening database
  RANSACKABLE_ATTRIBUTES = %w[title description].freeze
  def self.ransackable_attributes(_auth_object = nil)
    RANSACKABLE_ATTRIBUTES + _ransackers.keys
  end
```
* Enter the command "test1".  The test should pass.

### Correcting Offenses
* Enter the command "sh git_check.sh".  All tests should pass, but there will be offenses.
* Edit the file config/rails_best_practices.yml.  For MoveModelLogicIntoModelCheck, add the file app/controllers/openings_controller.rb to the list of ignored files.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Trying Out the Search Engine
In your local browser, view your app and try out the job opening search engine.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the opening index search capability"
git push origin 06-04-opening_index_search
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Hakiri will flag the app/views/opening/index.html.erb file for a dynamic render path because of parameter values in the path name.  Mark this as a false positive.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
