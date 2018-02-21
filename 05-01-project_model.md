# Unit 5
## Chapter 1: For Hire Model

### New Branch
Enter the command "git checkout -b 05-01-project_model".

### Adding the Project Object
* Enter the following command:
```
rails generate model project title:string source_code_url:string deployed_url:string description:text user:references
```
* Like the for_hire object, the project object belongs to the user object.
* In the db/migrate/[timestamp]_create_projects.rb file, add the following line just before the second to last "end" statement (after the create_table loop):
```
    add_index :projects, [:user_id, :created_at]
```
* Please note that the above migration uses the combination of the creation time and the ID of the associated user to index the project objects.
* Please note that if you don't do things right, you will be bombarded with a long cascade of cryptic error messages about PostgreSQL foreign key violations.  Neglecting the above change in the migration script is one factor that will lead to this.
* Enter the command "rails db:migrate".
* Enter the command "sh testm.sh".  You will get a long cascade of error messages about PostgreSQL foreign key violations.  The other factor that leads to this is the initial default test fixtures of the for_hire class, which belong to user objects that are not provided in the user test fixtures.
* Remove the project test fixtures by entering the command "rm test/fixtures/projects.yml".  Again, this is necessary to avoid the PostgreSQL foreign key violations.
* Edit the app/models/project.rb file.  Add the line "#" just before the line "class Project < ApplicationRecord".
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added the project object"
```

### Model Test
* Edit the file test/models/project_test.rb.  Replace everything between the line "class ProjectTest < ActiveSupport::TestCase" and the last "end" statement with the following:
```
  def setup
    @user = users(:connery)
    url_s = 'https://github.com/sconnery/anti_spectre'
    url_d = 'http://www.antispectre.com'
    @p = @user.projects.create(title: 'Anti-SPECTRE',
                               description: "Blow up Blofeld's hideout",
                               source_code_url: url_s,
                               deployed_url: url_d)
  end

  test 'should be valid' do
    assert @p.valid?
  end

  test 'must have description' do
    @p.description = ''
    assert_not @p.valid?
  end

  test 'must have title' do
    @p.title = ''
    assert_not @p.valid?
  end

  test 'description should not be too long' do
    @p.description = 'a' * 4096
    assert_not @p.valid?
    @p.description = 'a' * 4095
    assert @p.valid?
  end

  test 'title should not be too long' do
    @p.title = 'a' * 256
    assert_not @p.valid?
    @p.title = 'a' * 255
    assert @p.valid?
  end

  test 'source_code_url should not be too long' do
    @p.source_code_url = 'http://www.' + 'a' * 241 + '.com'
    assert_not @p.valid?
    @p.source_code_url = 'http://www.' + 'a' * 240 + '.com'
    assert @p.valid?
  end

  test 'deployed_url should not be too long' do
    @p.deployed_url = 'http://www.' + 'a' * 241 + '.com'
    assert_not @p.valid?
    @p.deployed_url = 'http://www.' + 'a' * 240 + '.com'
    assert @p.valid?
  end

  test 'deleting the user should delete the associated projects' do
    assert_difference 'Project.count', -1 do
      @user.destroy
    end
  end
```
* Enter the command "sh testm.sh".  All 8 tests fail because projects is undefined in the user model.
* Edit the file app/models/user.rb.  At the end of the public section, add the following line:
```
  has_many :projects, dependent: :destroy
```
* Enter the command "sh testm.sh".  The first and last tests now pass, but the other 6 still fail.
* Edit the app/models/project.rb file.  Just before the "end" statement, add the following code:
```
  validates :user_id, presence: true
  validates :description, presence: true, length: { maximum: 4095 }
  validates :title, presence: true, length: { maximum: 255 }
  validates :source_code_url, length: { maximum: 255 }
  validates :deployed_url, length: { maximum: 255 }
```
* Enter the command "sh testm.sh".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no violations.
* Enter the following commands:
```
git add .
git commit -m "Passes the project model tests"
```

### Seeding
* Edit the file db/seeds.rb.  Add the following code to the end of the script:
```

##########################
# BEGIN: creating projects
##########################
puts 'Creating projects'
def create_project(user_n, n)
  t = "Title #{n}: #{Faker::Company.catch_phrase}"
  url_s = Faker::Internet.url
  url_d = Faker::Internet.url
  d = "Description #{n}: #{Faker::Lorem.paragraph(10)}"
  @p = user_n.projects.create(title: t,
                              source_code_url: url_s,
                              deployed_url: url_d,
                              description: d)
end

title1 = 'Project Argus'
url_1a = 'https://github.com/argus/find_aliens'
url_1b = 'http://www.projectargus.org'
d1 = 'Finding radio signals from alien beings'
user1.projects.create(title: title1, source_code_url: url_1a,
                      deployed_url: url_1b, description: d1)

title2 = 'Dodecahedron'
url_2a = 'https://github.com/argus/dodecahedron'
url_2b = 'http://www.dodecahedron.org'
d2 = 'Building the spacecraft with the instructions from the aliens'
user1.projects.create(title: title2, source_code_url: url_2a,
                      deployed_url: url_2b, description: d2)

# Other users
n = 0
users.each do |u|
  n += 1
  next if rand < 0.2 || u == user1
  n_projects = rand(1..3)
  n_projects.times do
    @p = create_project(u, n)
    n += 1
  end
end

########################
# END: creating projects
########################
```
* Go to the tmux window containing your local Rails server. Press Ctrl-C to stop the server, and then enter the command "sh seed.sh; sh server.sh".  Wait for these scripts to finish executing.
* Use pgAdmin or the Rails sandbox to verify that the database has been seeded with the for hire profiles.
* Enter the command "sh git_check.sh". All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Updated the seeding script to add projects"
git push origin 05-01-project_model
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
