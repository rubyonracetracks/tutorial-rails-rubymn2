# Unit 6
## Chapter 1: Opening Model

### New Branch
Enter the command "git checkout -b 06-01-opening_model".

### Adding the Opening Object
* Enter the following command:
```
rails generate model opening title:string description:text user:references
```
* Like the forhire object, the opening object belongs to the user object.
* In the db/migrate/[timestamp]_create_openings.rb file, add the following line just before the second to last "end" statement (after the create_table loop):
```
    add_index :openings, [:user_id, :created_at]
```
* Please note that the above migration uses the combination of the creation time and the ID of the associated user to index the opening objects.
* Please note that if you don't do things right, you will be bombarded with a long cascade of cryptic error messages about PostgreSQL foreign key violations.  Neglecting the above change in the migration script is one factor that will lead to this.
* Enter the command "rails db:migrate".
* Enter the command "sh testm.sh".  You will get a long cascade of error messages about PostgreSQL foreign key violations.  The other factor that leads to this is the initial default test fixtures of the forhire class, which belong to user objects that are not provided in the user test fixtures.
* Remove the opening test fixtures by entering the command "rm test/fixtures/openings.yml".  Again, this is necessary to avoid the PostgreSQL foreign key violations.
* Edit the app/models/opening.rb file.  Add the line "#" just before the line "class Opening < ApplicationRecord".
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the opening object"
```

### Model Test
* Edit the file test/models/opening_test.rb.  Replace everything between the line "class OpeningTest < ActiveSupport::TestCase" and the last "end" statement with the following:
```
  def setup
    @user = users(:justice)
    @op = @user.openings.create(title: 'Deputy',
                                description: "Help catch the Bandit!")
  end

  test 'should be valid' do
    assert @op.valid?
  end

  test 'must have description' do
    @op.description = ''
    assert_not @op.valid?
  end

  test 'must have title' do
    @op.title = ''
    assert_not @op.valid?
  end

  test 'description should not be too long' do
    @op.description = 'a' * 4096
    assert_not @op.valid?
    @op.description = 'a' * 4095
    assert @op.valid?
  end

  test 'title should not be too long' do
    @op.title = 'a' * 256
    assert_not @op.valid?
    @op.title = 'a' * 255
    assert @op.valid?
  end

  test 'deleting the user should delete the associated openings' do
    assert_difference 'Opening.count', -1 do
      @user.destroy
    end
  end
```
* Enter the command "sh testm.sh".  All 5 tests fail because openings is undefined in the user model.
* Edit the file app/models/user.rb.  At the end of the public section, add the following line:
```
  has_many :openings, dependent: :destroy
```
* Enter the command "sh testm.sh".  The first and last tests now pass, but the other 3 still fail.
* Edit the app/models/opening.rb file.  Just before the "end" statement, add the following code:
```
  validates :user_id, presence: true
  validates :description, presence: true, length: { maximum: 4095 }
  validates :title, presence: true, length: { maximum: 255 }
```
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no violations.
* Enter the following commands:
```
git add .
git commit -m "Passes the opening model tests"
```

### Seeding
* Edit the file db/seeds.rb.  Add the following code to the end of the script:
```

##########################
# BEGIN: creating openings
##########################
puts 'Creating openings'
def create_opening(user_n, n)
  t = "Opening #{n}: #{Faker::Job.title}"
  d = "Description #{n}: #{Faker::Lorem.paragraph(10)}"
  @op = user_n.openings.create(title: t, description: d)
end

title1 = 'Radio signal analysis engineer'
d1 = 'Find the information in received radio waves'
user1.openings.create(title: title1, description: d1)

title2 = 'Computational engineer'
d2 = 'Calculate pi with increasingly perfect accuracy'
user1.openings.create(title: title2, description: d2)

# Other users
n = 0
users.each do |u|
  n += 1
  next if rand < 0.2 || u == user1
  n_openings = rand(1..3)
  n_openings.times do
    @op = create_opening(u, n)
    n += 1
  end
end

########################
# END: creating openings
########################
```
* Go to the tmux window containing your local Rails server. Press Ctrl-C to stop the server, and then enter the command "sh seed.sh; sh server.sh".  Wait for these scripts to finish executing.
* Use pgAdmin or the Rails sandbox to verify that the database has been seeded with the opening profiles.
* Enter the command "sh git_check.sh". All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Updated the seeding script to add openings"
git push origin 06-01-opening_model
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
