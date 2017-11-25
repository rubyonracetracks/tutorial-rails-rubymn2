# Unit 3
## Chapter 8: Uploading Sponsor Logos

In this chapter, you will enable super admins to upload a logo when creating or editing a sponsor.

### New Branch
Enter the command "git checkout -b 03-08-sponsor_logo_upload".

### Downloading Sponsor Logos
* Download the Debian Linux logo image by entering the following LONG one-line command:
```
curl -o app/assets/images/debian_logo.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/debian_logo.png
```
* Download the Big Buy logo image by entering the following LONG one-line command:
```
curl -o app/assets/images/big_buy_logo.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/big_buy_logo.png
```
* Download the Best Buy logo image by entering the following LONG one-line command:
```
curl -o app/assets/images/Best_Buy_Logo.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/Best_Buy_Logo.png
```

### Integration Test
* Enter the command "rails generate integration_test sponsor_logo_upload".
* Edit the test/integration/sponsor_logo_upload_test.rb file.  Replace everything between the line "class SponsorLogoUploadTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  # Xpath string used for testing for images
  def xpath_input_img(url)
    str1 = './/img[@src="'
    str2 = url
    str3 = '"]'
    output = "#{str1}#{str2}#{str3}"
    output
  end

  # fn: filename of image file
  def edit_logo(fn)
    basename = File.basename fn
    
    login_as(@a1, scope: :admin)
    visit sponsor_path(@sponsor1)
    click_on 'Edit Sponsor'
    find('form input[type="file"]').set(Rails.root + fn)
    click_button('Submit')

    assert page.has_text?('Sponsor updated')
    url = "/uploads/sponsor/picture/#{@sponsor1.id}/#{basename}"
    page.assert_selector(:xpath, xpath_input_img(url))
  end

  test 'super admin can add logo when creating sponsor' do
    login_as(@a1, scope: :admin)
    visit sponsors_path

    # Add current sponsor
    click_on 'Add Sponsor'
    assert page.has_css?('title', text: full_title('Add Sponsor'),
                                  visible: false)
    assert page.has_css?('h1', text: 'Add Sponsor')
    fill_in('Name', with: 'Debian Linux')
    fill_in('Phone', with: '800-555-1357')
    fill_in('Description', with: 'Universal operating system')
    fill_in('Email', with: 'info@debian.org')
    fill_in('URL', with: 'http://www.debian.org')
    check('Current')

    filename = 'app/assets/images/debian_logo.png'
    find('form input[type="file"]').set(Rails.root + filename)

    click_button('Submit')
    
    click_on 'Debian Linux'
    assert page.has_css?('h1', text: 'Current Sponsor: Debian Linux')

    s = Sponsor.find_by(name: 'Debian Linux')
    url = "/uploads/sponsor/picture/#{s.id}/debian_logo.png"
    page.assert_selector(:xpath, xpath_input_img(url))
  end

  test 'super admin can edit the sponsor logo' do
    f1 = 'app/assets/images/big_buy_logo.png'
    edit_logo(f1)
    f2 = 'app/assets/images/Best_Buy_Logo.png'
    edit_logo(f2)
  end
```

### Sponsor Controller
Edit the file app/controllers/sponsors_controller.rb. In the sponsor_params definition, add ":picture" to the list of keys.

### Edit Sponsor Page
* In the app/views/sponsors/edit.html.erb file, add the following lines immediately before the Submit button:
```
      <div class="field">
        <span class="picture">
          <b>OPTIONAL: Upload sponsor logo</b>
          <%= f.file_field :picture, accept: 'image/jpeg,image/gif,image/png' %>
        </span>
      </div>
```

### Add Sponsor Page
In the app/views/sponsors/new.html.erb file, add the following lines immediately before the Submit button:
```
      <div class="field">
        <span class="picture">
          <b>OPTIONAL: Upload sponsor logo</b>
          <%= f.file_field :picture, accept: 'image/jpeg,image/gif,image/png' %>
        </span>
      </div>
```

### Wrapping Up
* Enter the command "git push origin 03-08-sponsor_logo_upload".
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* Accept this pull request to merge it with the master branch, but do NOT delete this branch.
* Enter the following commands:
```
git checkout master
git pull
sh heroku.sh
```
