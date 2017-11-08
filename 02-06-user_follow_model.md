# Unit 2
## Chapter 6: User Relationship Model

In this chapter, you will add the relationship model that allows users to follow other users.

### New Branch
Enter the command "git checkout -b 02-06-follower_model".

### Generating the Relationship Model
* Enter the following command:
```
rails generate model Relationship follower_id:integer followed_id:integer
```
* The combination of follower user and followed user will be the defining parameters of each relationship.  These two users are defined by their ID numbers in the database.  No two relationships may have the same combination.
* Edit the file db/migrate/[timestamp]_create_relationships.rb.  Just before the end of the change method, add the following lines:
```
    add_index :relationships, :follower_id
    add_index :relationships, :followed_id
    add_index :relationships, [:follower_id, :followed_id], unique: true
```
* Enter the command "rails db:migrate".

### Relationship Model Test
* Edit the file test/models/relationship_test.rb.  Replace everything between "RelationshipTest < ActiveSupport::TestCase" and the last "end" statement with the following:
```
  def setup
    @relationship = Relationship.new(follower_id: users(:moore).id,
                                     followed_id: users(:connery).id)
  end

  test 'should be valid' do
    assert @relationship.valid?
  end

  test 'should require a follower_id' do
    @relationship.follower_id = nil
    assert_not @relationship.valid?
  end

  test 'should require a followed_id' do
    @relationship.followed_id = nil
    assert_not @relationship.valid?
  end

  test 'should be unique' do
    @relationship_dup = Relationship.new(follower_id: users(:moore).id,
                                         followed_id: users(:connery).id)
    assert_not @relationship_dup.valid?
  end
```
* Enter the command "sh testm.sh".  32 tests fail.
* Edit the file app/models/user.rb.  Add the following lines immediately after the line "class User < ApplicationRecord":
```
  has_many :active_relationships, class_name:  'Relationship',
                                  foreign_key: 'follower_id',
                                  dependent:   :destroy
```
* In the app/models/relationship.rb file, add the line "#" immediately before the line "class Relationship < ApplicationRecord".
* In file app/models/relationship.rb, add the following lines immediately after the line "class Relationship < ApplicationRecord":
```
  belongs_to :follower, class_name: "User"
  belongs_to :followed, class_name: "User"
  validates :follower_id, presence: true
  validates :followed_id, presence: true
```
* Enter the command "sh testm.sh".  Now only one test should fail, the uniqueness test.







* Enter the command "rails console" to enter the Rails console
* Enter the command "ActiveRecord::Migration.drop_table(:relationships)".  This erases the relationships table in the database.  (If you neglect this step, the "rails db:migrate" command won't work.)
* Enter the command "exit" to leave the Rails console.
* Enter the command "rails db:migrate; sh testm.sh".


### Updating the User Model Tests



* Remove the default relationship test fixtures by entering the following command:
```
echo '# empty' > test/fixtures/relationships.yml
```
* Enter the command "sh testm.sh".

### Updating the Relationship Migration Table


### Updating the User Model Test

### Initial Model Updates
* Edit the file app/models/user.rb.  Just before the end of the public section, add the following lines:
```
  has_many :active_relationships, class_name:  'Relationship',
                                  foreign_key: 'follower_id',
                                  dependent:   :destroy
```
* Note that destroying the user automatically destroys the relationship.
* Edit the file app/models/relationship.rb.  Just before the "end" command, add the following lines:
```
  belongs_to :follower, class_name: 'User'
  belongs_to :followed, class_name: 'User'
```

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the follower model"
git push origin 02-04-follower_model
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
