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
    click_on 'Add Sponsor'
    assert page.has_css?('title', text: full_title('Edit Sponsor'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Edit Sponsor')
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
