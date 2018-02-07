# Unit 4
## Chapter 4: Forhire Index Search

In this chapter, you will add a search engine to the forhire index page.

### New Branch
Enter the command "git checkout -b 04-04-forhire_index_search".

### Routing
Edit the config/routes.rb file.  Just before the end of the forhire section, add the following code:
```
  resources :forhires do
    collection { post :search, to: 'forhires#index' }
  end
```

### Controller
Edit the file app/controllers/forhires_controller.rb.  Replace the definition of "index" with the following code:
```
  # rubocop:disable Metrics/AbcSize
  def index
    @search = Forhire.search(params[:q].presence)
    @forhires = @search.result.paginate(page: params[:page])
    # NOTE: The following line specifies the sort order.
    # This is reflected in the default sort criteria shown.
    # The visitor is free to remove these default criteria.
    @search.sorts = 'updated_at desc' if @search.sorts.empty?
    @search.build_condition if @search.conditions.empty?
    @search.build_sort if @search.sorts.empty?
    @forhires = @search.result
    @forhires = @forhires.order('updated_at DESC').page(params[:page])
  end
  # rubocop:enable Metrics/AbcSize
```

### Index Page
Edit the file app/views/forhires/index.html.erb.  Add the following code immediately after the heading "For Hire Index":
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

### Fields and Conditions
Add the file app/views/forhires/_condition_fields.html.erb.  Give it the following content:
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
