# Unit 4
## Chapter 4: For Hire Model

### New Branch
Enter the command "git checkout -b 04-01-forhire_model".

### Model Test
* Enter the following command:
```
rails generate model for_hire blurb:text email:string title:string user:references
```
* Note that the "user:references" portion of the above command connects this object to a user object.
* Enter the command "rails db:migrate".
* Edit the file test/fixtures/for_hires.yml.  Replace the default test fixtures with the following content:
```
bond_lazenby:
  blurb: 'I was James Bond for one movie.'
  email: 'george_lazenby@rubyonracetracks.com'
  title: 'James Bond 1969'
  user: lazenby
  created_at: <%= Time.new(1910, 1, 1) %>
  updated_at: <%= Time.new(1969, 12, 18) %>

bond_moore:
  blurb: 'I made James Bond campier.'
  email: 'roger_moore@rubyonracetracks.com'
  title: 'James Bond 1973-1985'
  user: moore
  created_at: <%= Time.new(1909, 1, 1) %>
  updated_at: <%= Time.new(1985, 5, 22) %>

bond_dalton:
  blurb: 'I brought gritty realism to the Bond series.'
  email: 'timothy_dalton@rubyonracetracks.com'
  title: 'James Bond 1987-1989'
  user: dalton
  created_at: <%= Time.new(1908, 1, 1) %>
  updated_at: <%= Time.new(1989, 6, 13) %>

bond_brosnan:
  blurb: 'James Bond moved beyond the Cold War Era on my watch.'
  email: 'pierce_brosnan@rubyonracetracks.com'
  title: 'James Bond 1995-2002'
  user: dalton
  created_at: <%= Time.new(1907, 1, 1) %>
  updated_at: <%= Time.new(2002, 11, 20) %>

bond_craig:
  blurb: 'I rebooted James Bond.'
  email: 'daniel_craig@rubyonracetracks.com'
  title: 'James Bond 2006-'
  user: dalton
  created_at: <%= Time.new(1906, 1, 1) %>
  updated_at: <%= Time.now %>
```
* Edit the file test/models/for_hire_test.rb.  Replace everything between the line "class ForHireTest < ActiveSupport::TestCase" and the last "end" statement with the following:
```
  def setup
    @user = users(:connery)
    @for_hire = ForHire.new(blurb: 'I stopped Blofeld 4 times!',
                            email: 'first_bond@rubyonracetracks.com',
                            title: 'James Bond 1962-1971',
                            user_id: @user.id,
                            confirmed_at: Time.new(1962, 10, 5),
                            updated_at: Time.new(1971, 12, 14))
  end

  test 'should be valid' do
    assert @for_hire.valid?
  end

  test 'must have blurb' do
    @for_hire.blurb = ''
    assert_not @for_hire.valid?
  end

  test 'must have email' do
    @for_hire.email = ''
    assert_not @for_hire.valid?
  end

  test 'must have title' do
    @for_hire.title = ''
    assert_not @for_hire.valid?
  end

  test 'blurb should be no more than 4095 characters long' do
    @for_hire.blurb = 'a' * 4096
    assert_not @for_hire.valid?
    @for_hire.blurb = 'a' * 4095
    assert @for_hire.valid?
  end

  test 'email should not be too long' do
    @for_hire.email = 'a' * 244 + '@example.com'
    assert_not @for_hire.valid?
    @for_hire.email = 'a' * 243 + '@example.com'
    assert @for_hire.valid?
  end

  test 'title should not be too long' do
    @for_hire.title = 'a' * 256
    assert_not @for_hire.valid?
    @for_hire.title = 'a' * 255
    assert @for_hire.valid?
  end

  test 'user may have only one for_hire object' do
    @for_hire_2 = ForHire.new(blurb: 'I stopped Largo twice!',
                              email: 'nsna@example.com',
                              title: 'James Bond 1983',
                              user_id: @user.id)
    assert_not @for_hire_2.valid?
  end

  test 'order should be correct' do
    assert_equal ForHire(:bond_lazenby), ForHire.first
    assert_equal ForHire(:bond_craig), ForHire.last
  end
```
* Enter the command "sh testm.sh".  The last 8 new model tests fail.
* Edit the app/models/for_hire.rb file.  Add the line "#" just before the line "class ForHire < ApplicationRecord".
* Edit the app/models/for_hire.rb file.  Just before the "end" statement, add the following code:
```
  validates :blurb, presence: true, length: { maximum: 4095 }
  validates :email, presence: true, length: { maximum: 255 }
  validates :title, presence: true, length: { maximum: 255 }
```
* Edit the app/models/user.rb file.  Add the following line to the end of the public section:
```
  has_one :for_hire, :dependent => :destroy
```

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the for hire model"
git push origin 04-01-forhire_model
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
