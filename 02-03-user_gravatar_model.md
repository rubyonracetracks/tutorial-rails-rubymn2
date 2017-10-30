# Unit 2
## Chapter 3: User Gravatar Parameter

In this chapter, you will add the gravatar email address to the user model and then update the seeding script.

### New Branch
Enter the command "git checkout -b 02-03-user_gravatar_model".

### Adding the Parameter
* Enter the following command:
```
rails generate migration add_gravatar_email_to_users gravatar_email:string
```
* Enter the command "rails db:migrate".

### Updating the User Model Test
* Add the following lines just before the final "end" in test/models/user_test.rb:
```
  test 'gravatar_email should not be too long' do
    @user.gravatar_email = 'a' * 244 + '@example.com'
    assert_not @user.valid?
    @user.gravatar_email = 'a' * 243 + '@example.com'
    assert @user.valid?
  end

  test 'gravatar_email validation should accept valid addresses' do
    valid_addresses = %w[user@example.com USER@foo.COM A_US-ER@foo.bar.org
                         first.last@foo.jp alice+bob@baz.cn]
    valid_addresses.each do |valid_address|
      @user.gravatar_email = valid_address
      assert @user.valid?, "#{valid_address.inspect} should be valid"
    end
  end

  test 'gravatar_email validation should reject invalid addresses' do
    invalid_addresses = %w[user@example,com user_at_foo.org user.name@example.
                           foo@bar_baz.com foo@bar+baz.com]
    invalid_addresses.each do |invalid_address|
      @user.gravatar_email = invalid_address
      assert_not @user.valid?, "#{invalid_address.inspect} should be invalid"
    end
  end
```
* Add the following lines to the end of the file .rubocop.yml:
```
Metrics/ClassLength:
  Exclude:
    - test/models/user_test.rb
```
* Enter the command "sh testm.sh".  2 of the user model tests fail.  The tests for screening out invalid gravatar_email addresses and excessively long gravatar_email addresses are the ones that fail.
* Add the following lines to app/models/user.rb immediately after the "validates :email" statement:
```
  validates :gravatar_email, length: { maximum: 255 },
                             allow_nil: true,
                             format: { with: VALID_EMAIL_REGEX }
```
* Enter the command "sh testm.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Seeding
* In the db/seeds.rb file, update the "Example User" to have the gravatar email address "example@railstutorial.org".
* In the db/seeds.rb file, update the random users to have the same gravatar email addresses as their regular email addresses.
* Go to the tmux window where the local Rails server is running.  Stop the local Rails server.  (If you don't, the seeding process will not work.)
* Enter the command "sh kill_spring.sh; sh seed.sh; sh server.sh".  This restarts the Spring server, reseeds the database, and restarts the local server.
* Use pgAdmin to view the database and verify that the seeding process worked as expected.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the gravatar_email parameter to the user model"
git push origin 02-03-user_gravatar_model
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
