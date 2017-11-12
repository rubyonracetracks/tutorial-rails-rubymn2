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
                           description: 'Exceptional talent. Refreshingly personal services.',
                           contact_email: 'valere@rubyonracetracks.com',
                           contact_url: 'http://valere.com/',
                           current: true)
  end

  test 'should be valid' do
    assert @user.valid?
  end

  test 'name should be less than 255' do
    @sponsor.name = 'a' * 244 + '@example.com'
    assert @user.valid?
  end
```


### Seeding

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
