# Unit 4
## Chapter 4: For Hire Model

### Special Note
In this app, there should be only one For Hire profile per user.  This limit will be enforced at the controller level in a later chapter.  I was unable to get the has_one association to work and could not find any good examples on how to do this.  Thus, I was forced to use the tried-and-true has_many association instead.  Attempts to limit the number of For Hire profiles per user at the model level did not pan out even though old StackOverflow posts suggested that this was possible.

### New Branch
Enter the command "git checkout -b 04-01-forhire_model".

### Adding the ForHire Object
* Enter the following command:
```
rails generate model forhire description:text email:string title:string user:references
```
* Note that the "user:references" portion of the above command connects the new for_hire object to a user object.  (The for_hire object belongs to the user object.)
* In the db/migrate/[timestamp]_create_forhires.rb file, add the following line just before the second to last "end" statement (after the create_table loop):
```
    add_index :forhires, [:user_id, :created_at]
```
* Please note that the above migration uses the combination of the creation time and the ID of the associated user to index the for_hires objects.
* Please note that if you don't do things right, you will be bombarded with a long cascade of cryptic error messages about PostgreSQL foreign key violations.  Neglecting the above change in the migration script is one factor that will lead to this.
* Enter the command "rails db:migrate".
* Enter the command "sh testm.sh".  You will get a long cascade of error messages about PostgreSQL foreign key violations.  The other factor that leads to this is the initial default test fixtures of the for_hire class, which belong to user objects that are not provided in the user test fixtures.
* Remove the forhires test fixtures by entering the command "rm test/fixtures/forhires.yml".  For reasons that will be explained later, forhires test fixtures will not be used.
* Edit the app/models/forhire.rb file.  Add the line "#" just before the line "class ForHire < ApplicationRecord".
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the forhire object"
```

### Model Test
* Edit the file test/models/forhire_test.rb.  Replace everything between the line "class ForhireTest < ActiveSupport::TestCase" and the last "end" statement with the following:
```
  def setup
    @user = users(:connery)
    @fh = @user.forhires.create(description: 'I stopped Blofeld 4 times!',
                                email: 'first_bond@rubyonracetracks.com',
                                title: 'James Bond 1962-1971')
  end

  test 'should be valid' do
    assert @fh.valid?
  end

  test 'must have description' do
    @fh.description = ''
    assert_not @fh.valid?
  end

  test 'must have email' do
    @fh.email = ''
    assert_not @fh.valid?
  end

  test 'must have title' do
    @fh.title = ''
    assert_not @fh.valid?
  end

  test 'description should not be too long' do
    @fh.description = 'a' * 4096
    assert_not @fh.valid?
    @fh.description = 'a' * 4095
    assert @fh.valid?
  end

  test 'email should not be too long' do
    @fh.email = 'a' * 244 + '@example.com'
    assert_not @fh.valid?
    @fh.email = 'a' * 243 + '@example.com'
    assert @fh.valid?
  end

  test 'title should not be too long' do
    @fh.title = 'a' * 256
    assert_not @fh.valid?
    @fh.title = 'a' * 255
    assert @fh.valid?
  end

  test 'deleting the user should delete the associated forhires' do
    assert_difference 'Forhire.count', -1 do
      @user.destroy
    end
  end
```
* Enter the command "sh testm.sh".  All 8 tests fail because forhires is undefined in the user model.
* Edit the file app/models/user.rb.  At the end of the public section, add the following line:
```
  has_many :forhires, dependent: :destroy
```
* Enter the command "sh testm.sh".  The first and last tests now pass, but the other 6 still fail.
* Edit the app/models/for_hire.rb file.  Just before the "end" statement, add the following code:
```
  validates :user_id, presence: true
  validates :description, presence: true, length: { maximum: 4095 }
  validates :email, presence: true, length: { maximum: 255 }
  validates :title, presence: true, length: { maximum: 255 }
```
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no violations.
* Enter the following commands:
```
git add .
git commit -m "Passes the forhire model tests"
```

### Seeding
* Edit the file db/seeds.rb.  Add the following code to the end of the script:
```
# DEFINE USERS
user1 = User.first # First user
users = User.all # All users

##########################
# BEGIN: creating forhires
##########################
puts 'Creating forhires'
def create_forhire(user_n, n)
  t = "Title #{n}: #{Faker::Name.title}"
  e = Faker::Internet.email
  d = "Description #{n}: #{Faker::Lorem.paragraph(10)}"
  @fh = user_n.forhires.create(title: t, email: e, description: d)
end

title1 = 'Galactic Traveler'
email1 = 'ellie_arroway@projectargus.org'
desc1 = 'I traveled around the galaxy in a dodecahedron.'
user1.forhires.create(title: title1, email: email1, description: desc1)

# Other users
n = 0
users.each do |u|
  n += 1
  next if rand < 0.2 || u == users.first
  @fh1 = create_forhire(u, n)
end

########################
# END: creating forhires
########################
```
* Go to the tmux window containing your local Rails server. Press Ctrl-C to stop the server, and then enter the command "sh seed.sh; sh server.sh".  Wait for these scripts to finish executing.
* Use pgAdmin or the Rails sandbox to verify that the database has been seeded with the for hire profiles.
* Enter the command "sh git_check.sh". All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Updated the seeding script to add forhire profiles"
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
