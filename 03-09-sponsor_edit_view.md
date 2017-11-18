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
    fill_in('Name', with: 'Coop's Beer')
    fill_in('Phone', with: '888-555-0111')
    fill_in('Description', with: "parody of Coor's Beer")
    fill_in('Email', with: 'info@coopsbeer.com')
    fill_in('URL', with: 'http://www.coopsbeer.com')
    check('Current')
    click_button('Submit')
    click_on "Coop's Beer"
    assert page.has_css?('h1', text: "Current Sponsor: Coop's Beer")
    assert page.has_text?('888-555-0111')
    assert page.has_text?('parody of Coor's Beer')
    assert page.has_text?('info@coopsbeer.com')
    assert page.has_text?('http://www.coopsbeer.com')
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
