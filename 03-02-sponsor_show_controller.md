# Unit 3
## Chapter 2: Sponsor Show Controller

In this chapter, you will add the show method in the sponsor controller.

### New Branch
Enter the command "git checkout -b 03-02-sponsor_show_controller".

### Controller Test
* Enter the command "rails generate controller Sponsors".
* Edit the file test/controllers/sponsors_controller_test.rb.  Replace everything between the line "class SponsorsControllerTest < ActionDispatch::IntegrationTest" and the last "end" command with the following:
```
  test 'sponsor show action' do
    get sponsor_path(@sponsor1)
    assert_response :success
    get sponsor_path(@sponsor2)
    assert_response :success
    get sponsor_path(@sponsor3)
    assert_response :success
    get sponsor_path(@sponsor4)
    assert_response :success
  end
```
* Enter the command "sh testc.sh".  The new controller test fails because sponsor_path is undefined.

### Routing
* Edit the config/routes.rb file.  Just before the last "end" statement, add the following line:
```
  resources :sponsors, only: [:show]
```
* Enter the command "sh testc.sh".  The new controller test fails because of missing routes.  (The sponsor objects are not yet provided.)

### Test Fixtures and Helper
* Add the following lines to the end of the file test/fixtures/sponsors.yml:
```
blessed_buy:
  name: 'Blessed Buy'
  phone: '(612) 555-0101'
  description: 'THE place to buy the new Galaxy Wars merchandise'
  contact_email: 'example@blessedbuy.com'
  contact_url: 'http://www.blessedbuy.com'
  current: true

sky_store:
  name: 'Sky Store'
  phone: '(800) 555-0102'
  description: 'If you fly, you can buy overpriced junk from our catalog.'
  contact_email: 'example@skystore.com'
  contact_url: 'http://www.skystore.com'
  current: true

island_hoppers:
  name: 'Island Hoppers'
  phone: '(808) 555-0103'
  description: 'I provide helicopter rides around the Hawaiian Islands.'
  contact_email: 'tc@islandhoppers.com'
  contact_url: 'http://www.islandhoppers.com'
  current: false

flag:
  name: 'Foundation for Law and Government'
  phone: '(800) 555-0104'
  description: 'We built Kitt.  Michael Knight and Kitt solve crimes.'
  contact_email: 'devon_miles@flag.org'
  contact_url: 'http://www.flag.org'
  current: false
```
* Edit the file test/test_helper.rb.  Just before the end of the definition of setup_universal, add the following lines:
```

  @sponsor1 = sponsors(:blessed_buy)
  @sponsor2 = sponsors(:sky_store)
  @sponsor3 = sponsors(:island_hoppers)
  @sponsor4 = sponsors(:flag)
```
* Enter the command "sh testc.sh".  The controller test fails because the "show" action is not in the sponsor controller yet.
* Edit the file app/controllers/sponsors_controller.rb.  Just before the line "class SponsorsController < ApplicationController", add the line "#".
* Edit the file app/controllers/sponsors_controller.rb.  Insert the following lines between "class SponsorsController < ApplicationController" and "end".
```
  # BEGIN: action section
  def show
    @sponsor = Sponsor.find(params[:id])
  end
  # END: action section
```
* Enter the command "sh testc.sh".  The controller test fails because of a missing template.
* Enter the command "touch app/views/sponsors/show.html.erb" to provide the template.  (You'll add content to it later.)
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "rm app/helpers/sponsors_helper.rb".
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor show controller"
git push origin 03-02-sponsor_show_controller
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
