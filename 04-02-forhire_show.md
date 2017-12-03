# Unit 4
## Chapter 2: Forhire Show Method
In this chapter, you will add the show method for the forhire object.

### New Branch
Enter the command "git checkout -b 04-02-forhire_show".

### Test Fixtures
* Edit the file test/fixtures/for_hires.yml.  Replace the default test fixtures with the following content:
```
bond_connery:
  blurb: 'I was the original James Bond.'
  email: 'sean_connery@rubyonracetracks.com'
  title: 'James Bond 1962-1971'
  user: lazenby
  created_at: <%= Time.new(1962, 10, 5) %>
  updated_at: <%= Time.new(1971, 12, 14) %>

bond_lazenby:
  blurb: 'I was James Bond for one movie.'
  email: 'george_lazenby@rubyonracetracks.com'
  title: 'James Bond 1969'
  user: lazenby
  created_at: <%= Time.new(1969, 12, 18, 0, 1) %>
  updated_at: <%= Time.new(1969, 12, 18, 23, 59) %>

bond_moore:
  blurb: 'I made James Bond campier.'
  email: 'roger_moore@rubyonracetracks.com'
  title: 'James Bond 1973-1985'
  user: moore
  created_at: <%= Time.new(1973, 6, 27) %>
  updated_at: <%= Time.new(1985, 5, 22) %>

bond_dalton:
  blurb: 'I brought gritty realism to the Bond series.'
  email: 'timothy_dalton@rubyonracetracks.com'
  title: 'James Bond 1987-1989'
  user: dalton
  created_at: <%= Time.new(1987, 6, 29) %>
  updated_at: <%= Time.new(1989, 6, 13) %>

bond_brosnan:
  blurb: 'James Bond moved beyond the Cold War Era on my watch.'
  email: 'pierce_brosnan@rubyonracetracks.com'
  title: 'James Bond 1995-2002'
  user: brosnan
  created_at: <%= Time.new(1995, 11, 13) %>
  updated_at: <%= Time.new(2002, 11, 20) %>

bond_craig:
  blurb: 'I rebooted James Bond.'
  email: 'daniel_craig@rubyonracetracks.com'
  title: 'James Bond 2006-'
  user: craig
  created_at: <%= Time.new(2006, 11, 14) %>
  updated_at: <%= Time.now %>
```

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the forhire show capability"
git push origin 04-02-forhire_show
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
