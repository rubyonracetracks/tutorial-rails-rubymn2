# Unit 4
## Chapter 4: Forhire Index Search

In this chapter, you will add a search engine to the forhire index page.

### New Branch
Enter the command "git checkout -b 04-04-forhire_index_search".

### Integration Test
* Enter the command "rails generate integration_test forhire_search".
* Edit the file test/integration/forhire_search_test.rb.  Replace everything between the line "class ForhireSearchTest < ActionDispatch::IntegrationTest" and the last end statement with the following code:
```
  # rubocop:disable Metrics/AbcSize
  # rubocop:disable Metrics/MethodLength
  test 'forhire search engine works' do
    add_extra_forhires
    visit forhires_path
    click_on 'For Hire'
    select 'Title', from: 'q[c][0][a][0][name]'
    select 'contains', from: 'q[c][0][p]'
    fill_in('q[c][0][v][0][value]', with: 'Daily stock market guru')
    puts page.body
    click_on 'Search'
    assert page.has_css?('title', text: full_title('For Hire Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'For Hire Index')
    first(:link, '2').click
    assert page.has_css?('title', text: full_title('For Hire Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'For Hire Index')
  end
  # rubocop:enable Metrics/AbcSize
  # rubocop:enable Metrics/MethodLength
```
* Enter the command "sh test_app.sh".  The new integration test will fail.
* Enter the command "alias test1='(Command to run failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The new integration test fails because the search engine elements are missing.

### Views
* Create the file app/views/forhires/_condition_fields.html.erb and give it the following content:
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
* Add the following code just before the h1 heading in app/views/forhires/index.html.erb
```
<br><br>

<%= search_form_for @search, url: search_forhires_path, method: :post do |f| %>
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
* Enter the command "test1".  Now your test fails because no ransack search object is provided.  The next step is to update the forhire controller

### Controller
* Edit the file app/controllers/forhires_controller.rb and replace the definition of the index action with the following code:
```
  # rubocop:disable Metrics/AbcSize
  def index
    @search = Forhire.search(params[:q].presence)
    # NOTE: The following line specifies the sort order.
    # This is reflected in the default sort criteria shown.
    # The user is free to remove these default criteria.
    @search.sorts = 'updated_at desc' if @search.sorts.empty?
    @search.build_condition if @search.conditions.empty?
    @search.build_sort if @search.sorts.empty?
    @forhires = @search.result
    @forhires_count = @forhires.count
    @forhires = @forhires.page(params[:page]).per(50)
  end
  # rubocop:enable Metrics/AbcSize
```
* Enter the command "test1".  Now the test fails because search_forhires_path is undefined.

### Routing
* Edit the config/routes.rb file.  Replace the line that begins with "resources :forhires" with the following:
```
  resources :forhires, only: [:show, :index] do
    root to: 'forhires#index'
    collection { post :search, to: 'forhires#index' }
  end
```
* Enter the command "test1".  Now the test fails because the Search button is not recognized.

### app/assets/javascripts/forhires.coffee
Add the following code to the end of the file app/assets/javascripts/forhires.coffee:
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

### Model
Edit the file app/models/forhire.rb.  Add the following code just before the last "end" statement:
```
  # Limit the parameters available for searching the forhire database
  RANSACKABLE_ATTRIBUTES = %w[title description email].freeze
  def self.ransackable_attributes(_auth_object = nil)
    RANSACKABLE_ATTRIBUTES + _ransackers.keys
  end
```

### Rails Best Practices
* Edit the file config/rails_best_practices.yml.  For MoveModelLogicIntoModelCheck, add the file app/controllers/forhires_controller.rb to the list of ignored files.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the forhire index search capability"
git push origin 04-04-forhire_index_search
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
