# Unit 3
# Chapter 1: Sponsor Models
In this chapter, you will create the sponsor model and seed the development environment database with simulated sponsors.

### New Branch
Enter the command "git checkout -b 03-01-sponsor_model".

### Model Test
* Enter the following command:
```
rails generate model Sponsor name:string phone:string description:text contact_email:string contact_url:string current:boolean 
```
* Enter the command "rails db:migrate".
* Edit the file test/models/sponsor_test.rb.  Replace everything between the lines "class SponsorTest < ActiveSupport::TestCase" and the final "end" command with the following:
```
  def setup
    @sponsor = Sponsor.new(name: 'Valere Consulting and Recruiting',
                           phone: '612-555-0100',
                           description: 'Exceptional talent',
                           contact_email: 'valere@rubyonracetracks.com',
                           contact_url: 'http://valere.com/',
                           current: true)
  end

  test 'should be valid' do
    assert @sponsor.valid?
  end

  test 'name must be present' do
    @sponsor.name = nil
    assert_not @sponsor.valid?
  end

  test 'name should be no more than 255 characters long' do
    @sponsor.name = 'a' * 256
    assert_not @sponsor.valid?
    @sponsor.name = 'a' * 255
    assert @sponsor.valid?
  end

  test 'phone number should be no more than 255 characters long' do
    @sponsor.phone = '5' * 256
    assert_not @sponsor.valid?
    @sponsor.phone = '5' * 255
    assert @sponsor.valid?
  end

  test 'description should be no more than 4095 characters long' do
    @sponsor.description = 'a' * 4096
    assert_not @sponsor.valid?
    @sponsor.description = 'a' * 4095
    assert @sponsor.valid?
  end

  test 'contact_email should be no more than 255 characters long' do
    @sponsor.contact_email = 'a' * 244 + '@example.com'
    assert_not @sponsor.valid?
    @sponsor.contact_email = 'a' * 243 + '@example.com'
    assert @sponsor.valid?
  end

  test 'contact_url should be no more than 255 characters long' do
    @sponsor.contact_url = 'http://www' + 'a' * 242 + '.com'
    assert_not @sponsor.valid?
    @sponsor.contact_url = 'http://www' + 'a' * 241 + '.com'
    assert @sponsor.valid?
  end
  ```
* Enter the command "sh testm.sh".  The last 6 new model tests fail.
* Edit the app/models/sponsor.rb file.  Add the line "#" just before the line "class Sponsor < ApplicationRecord".
* Edit the app/models/sponsor.rb file.  Insert the following code between the line "class Sponsor < ApplicationRecord" and "end":
```
  validates :name, presence: true, length: { maximum: 255 }
  validates :phone, length: { maximum: 255 }
  validates :description, length: { maximum: 4095 }
  validates :contact_email, length: { maximum: 255 }
  validates :contact_url, length: { maximum: 255 }
```
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Seeding
* Add the following lines to the end of the db/seeds.rb file:
```
##########################
# BEGIN: creating sponsors
##########################

def create_sponsor(i_sponsor, status_current)
  n1 = "Sponsor #{i_sponsor}: #{Faker::Company.name}"
  p1 = Faker::PhoneNumber.phone_number
  c1 = status_current
  d1 = "SPONSOR DESCRIPTION #{i_sponsor}: "
  d1 += Faker::Company.catch_phrase.to_s
  e1 = Faker::Internet.email
  u1 = Faker::Internet.url
  Sponsor.create!(name: n1, phone: p1,
                  description: d1, contact_email: e1, contact_url: u1,
                  current: c1)
end

# Create past sponsors
puts 'Creating past sponsors'
n = 0
5.times do
  n += 1
  create_sponsor(n, false)
end

# Create current sponsors
puts 'Creating current sponsors'
5.times do
  n += 1
  create_sponsor(n, true)
end

########################
# END: creating sponsors
########################
```
* Go to the tmux window containing your local Rails server.  Press Ctrl-C to stop the server, and then enter the command "sh seed.sh; sh server.sh".

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor model"
git push origin 03-01-sponsor_model
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
