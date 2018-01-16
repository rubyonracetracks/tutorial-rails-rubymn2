# Unit 2
## Chapter 7: User Relationship Model

In this chapter, you will add the relationship model that allows users to follow other users.  Please note that the uniqueness of relationship objects is enforced at the database migration level instead of at the model level.

### New Branch
Enter the command "git checkout -b 02-07-follower_model".

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

### Relationship Model Tests
* Edit the file test/models/relationship_test.rb.  Replace everything between "RelationshipTest < ActiveSupport::TestCase" and the last "end" statement with the following:
```
  def setup
    @relationship = Relationship.new(follower_id: users(:lazenby).id,
                                     followed_id: users(:connery).id)
  end

  test 'should be valid' do
    assert @relationship.valid?
  end

  test 'should allow the opposite relationship to exist as well' do
    @relationship_opp = Relationship.new(follower_id: users(:connery).id,
                                         followed_id: users(:lazenby).id)
    assert @relationship_opp.valid?
  end

  test 'should require a follower_id' do
    @relationship.follower_id = nil
    assert_not @relationship.valid?
  end

  test 'should require a followed_id' do
    @relationship.followed_id = nil
    assert_not @relationship.valid?
  end

  test 'deleting the follower deletes the relationship' do
    assert_difference 'Relationship.count', -2 do
      users(:lazenby).destroy
    end
  end

  test 'deleting the followed deletes the relationship' do
    assert_difference 'Relationship.count', -3 do
      users(:connery).destroy
    end
  end
```
* Enter the command "sh testm.sh".  You get a long cascade of errors because the initial relationship test fixtures violate the uniqueness requirement that you specified in the database migration script.  (The combination of follower_id and followed_id must be unique.)  Note that in the file test/fixtures/relationships.yml, the follower_id and followed_id in both relationships is 1.  Thus, the long cascade of errors is to be expected.
* In the file test/fixtures/relationships.yml, change the value of the followed_id to 2 for both fixtures.  (Keep the value of the follower_id at 1.)  Enter the command "sh testm.sh".  The 34 tests should fail again.
* In the file test/fixtures/relationships.yml, edit the values of the parameters in the relationship "two".  Change the value of the follower_id to 2 and the value of the followed_id to 1.  (This is the inverse of the values of the parameters in the relationship "one".)  Enter the command "sh testm.sh".  Now only the last 4 new model tests fail.  This provides confirmation that the modifications you made in the db/migrate/[timestamp]_create_relationships.rb file work at enforcing the uniqueness of each relationship.
* In the app/models/relationship.rb file, add the line "#" immediately before the line "class Relationship < ApplicationRecord".
* In file app/models/relationship.rb, add the following lines immediately after the line "class Relationship < ApplicationRecord":
```
  validates :follower_id, presence: true
  validates :followed_id, presence: true
```
* Enter the command "sh testm.sh".  Two tests fail.  Deleting the follower user or the followed user in a relationship does not yet automatically delete the relationship.  This must be addressed in the user model.

### User Model Tests
* Edit the file test/models/user_test.rb.  Add the following code just before the last "end" statement:
```
  test 'should be able to follow and unfollow a user' do
    blofeld = users(:blofeld)
    connery = users(:connery)
    assert_not blofeld.following?(connery)
    blofeld.follow(connery)
    assert blofeld.following?(connery)
    assert connery.followers.include?(blofeld)
    blofeld.unfollow(connery)
    assert_not blofeld.following?(connery)
  end
```
* Enter the command "sh testm.sh".  Three tests fail.
* Edit the file app/models/user.rb.  Add the following lines just before the end of the public section:
```
  # BEGIN: relationship section
  has_many :active_relationships,  class_name:  'Relationship',
                                   foreign_key: 'follower_id',
                                   dependent:   :destroy
  has_many :passive_relationships, class_name:  'Relationship',
                                   foreign_key: 'followed_id',
                                   dependent:   :destroy
  has_many :following, through: :active_relationships,  source: :followed
  has_many :followers, through: :passive_relationships, source: :follower

  # Follows a user
  def follow(other_user)
    following << other_user
  end

  # Unfollows a user
  def unfollow(other_user)
    following.delete(other_user)
  end

  # Returns true if the current user is following the other user.
  def following?(other_user)
    following.include?(other_user)
  end
  # END: relationship section
```
* Edit the file app/models/relationship.rb.  Add the following lines just after the line "class Relationship < ApplicationRecord":
```
  belongs_to :follower, class_name: 'User'
  belongs_to :followed, class_name: 'User'
```
* Replace the contents of test/fixtures/relationships.yml with the following content:
```
relationship1:
  follower: lazenby
  followed: connery

relationship2:
  follower: connery
  followed: lazenby

relationship3:
  follower: moore
  followed: connery

relationship4:
  follower: dalton
  followed: moore

relationship5:
  follower: brosnan
  followed: dalton

relationship6:
  follower: craig
  followed: brosnan
```
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Seeding
* Add the following code to the end of the db/seeds.rb file:
```
###############################
# BEGIN: creating relationships
###############################

def create_relationships(u)
  users = User.all
  puts '----------------------------------------'
  puts "creating relationships for #{u.username}"
  following = users[4..50]
  followers = users[5..40]
  following.each { |followed| u.follow(followed) }
  followers.each { |follower| follower.follow(u) }
end

create_relationships User.first
create_relationships User.second

##################################
# FINISHED: creating relationships
##################################
```
* In the tmux window for the local Rails server, stop the server.  Then enter the following command:
```
sh seed.sh; sh server.sh
```
* In the tmux window for entering commands, enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the follower model"
git push origin 02-07-follower_model
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
