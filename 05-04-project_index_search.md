# Unit 5
## Chapter 4: Project Index Search

In this chapter, you will add a search engine to the project index page.

### New Branch
Enter the command "git checkout -b 05-04-project_index_search".

### Integration Test
* Enter the command "rails generate integration_test project_search".
* Edit the file test/integration/project_search_test.rb.  Replace everything between the line "class ProjectSearchTest < ActionDispatch::IntegrationTest" and the last end statement with the following code:
```
  test 'project search engine works' do
    add_extra_projects
    visit projects_path
    click_on 'Projects'
    select 'Title', from: 'q[c][0][a][0][name]'
    select 'contains', from: 'q[c][0][p]'
    fill_in('q[c][0][v][0][value]', with: 'Project')
    click_on 'Search'
    assert page.has_css?('title', text: full_title('Project Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Project Index')
    first(:link, '2').click
    assert page.has_css?('title', text: full_title('Project Index'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Project Index')
  end
```
* Enter the command "sh test_app.sh".  The new integration test will fail.
* Enter the command "alias test1='(Command to run failed tests minus the TESTOPTS portion)'".
* Enter the command "test1".  The new integration test fails because the search engine elements are missing.

### Views
* Create the file app/views/projects/_condition_fields.html.erb and give it the following content:
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
* Add the following code just before the h1 heading in app/views/projects/index.html.erb
```
<br><br>

<%= search_form_for @search, url: search_projects_path, method: :post do |f| %>
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
* Enter the command "test1".  Now your test fails because search_projects_path is undefined.

### Routing
* Edit the config/routes.rb file.  Replace the line that begins with "resources :projects" with the following:
```
  resources :projects, only: [:show, :index] do
    root to: 'projects#index'
    collection { post :search, to: 'projects#index' }
  end
```
* Enter the command "test1". Now your test fails because no ransack search object is provided.

### Controller
* Edit the file app/controllers/projects_controller.rb and replace the definition of the index action with the following code:
```
  # rubocop:disable Metrics/AbcSize
  def index
    @search = Project.search(params[:q].presence)
    # NOTE: The following line specifies the sort order.
    # This is reflected in the default sort criteria shown.
    # The user is free to remove these default criteria.
    @search.sorts = 'updated_at desc' if @search.sorts.empty?
    @search.build_condition if @search.conditions.empty?
    @search.build_sort if @search.sorts.empty?
    @projects = @search.result
    @projects_count = @projects.count
    @projects = @projects.order('updated_at desc').page(params[:page]).per(50)
  end
  # rubocop:enable Metrics/AbcSize
```
* Enter the command "test1".  Now your test should pass, but you're not quite done yet.

### app/assets/javascripts/projects.coffee
* To allow the "Add conditions" and "remove" buttons to work, add the following code to the end of the file app/assets/javascripts/projects.coffee:
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
* It's time to limit the number of parameters available for searching the project database.  Edit the file app/models/project.rb.  Add the following code just before the last "end" statement:
```
  # Limit the parameters available for searching the project database
  RANSACKABLE_ATTRIBUTES = %w[title description].freeze
  def self.ransackable_attributes(_auth_object = nil)
    RANSACKABLE_ATTRIBUTES + _ransackers.keys
  end
```
* Enter the command "test1".  The test should pass.

### Correcting Offenses
* Enter the command "sh git_check.sh".  All tests should pass, but there will be offenses.
* Edit the file config/rails_best_practices.yml.  For MoveModelLogicIntoModelCheck, add the file app/controllers/projects_controller.rb to the list of ignored files.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Trying Out the Search Engine
In your local browser, view your app and try out the project search engine.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the project index search capability"
git push origin 05-04-project_index_search
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Hakiri will flag the app/views/project/index.html.erb file for a dynamic render path because of parameter values in the path name.  Mark this as a false positive.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
