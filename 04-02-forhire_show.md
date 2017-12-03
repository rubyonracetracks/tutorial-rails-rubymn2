

### Test Fixtures
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
  user: brosnan
  created_at: <%= Time.new(1907, 1, 1) %>
  updated_at: <%= Time.new(2002, 11, 20) %>

bond_craig:
  blurb: 'I rebooted James Bond.'
  email: 'daniel_craig@rubyonracetracks.com'
  title: 'James Bond 2006-'
  user: craig
  created_at: <%= Time.new(1906, 1, 1) %>
  updated_at: <%= Time.now %>
```
