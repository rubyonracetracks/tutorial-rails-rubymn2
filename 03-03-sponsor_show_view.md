# Unit 3
## Chapter 3: Sponsor Profiles

In this chapter, you will add sponsor profile pages.

### New Branch
Enter the command "git checkout -b 03-03-sponsor_show_view".

### Integration Test
* Enter the command "rails generate integration_test sponsor_show".
* 
```
    visit sponsor_path(@sponsor1)
    assert page.has_css?('title', text: full_title('Blessed Buy'), visible: false)
    assert page.has_css?('h1', text: 'Blessed Buy')
    assert_text '(612) 555-0101'
    assert_text 'THE place to buy the new Galaxy Wars merchandise'
    assert_text 'example@blessedbuy.com'
    assert_text 'http://www.blessedbuy.com'
    assert_text 'CURRENT SPONSOR'

    visit sponsor_path(@sponsor2)
    assert page.has_css?('title', text: full_title('Sky Store'), visible: false)
    assert page.has_css?('h1', text: 'Sky Store')
    assert_text '(800) 555-0102'
    assert_text 'If you fly, you can buy overpriced junk from our catalog.'
    assert_text 'example@skystore.com'
    assert_text 'http://www.skystore.com'
    assert_text 'CURRENT SPONSOR'

    visit sponsor_path(@sponsor3)
    assert page.has_css?('title', text: full_title('Island Hoppers'), visible: false)
    assert page.has_css?('h1', text: 'Island Hoppers')
    assert_text '(808) 555-0103'
    assert_text 'I provide helicopter rides around the Hawaiian Islands.'
    assert_text 'tc@islandhoppers.com'
    assert_text 'http://www.islandhoppers.com'
    
    visit sponsor_path(@sponsor4)
    assert page.has_css?('title', text: full_title('Foundation for Law and Government'), visible: false)
    assert page.has_css?('h1', text: 'Foundation for Law and Government')
    assert_text '(800) 555-0104'
    assert_text 'We built Kitt.  Michael Knight and Kitt solve crimes.'
    assert_text 'devon_miles@flag.org'
    assert_text 'http://www.flag.org'
```

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the sponsor profile pages"
git push origin 03-03-sponsor_show_view
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
