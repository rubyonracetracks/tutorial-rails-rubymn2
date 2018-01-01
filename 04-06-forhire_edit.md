# Unit 4
## Chapter 6: Forhire Edit

### New Branch
Enter the command "git checkout -b 04-06-forhire_edit".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/forhires_controller_test.rb. Add the following code immediately after the definition of create_forhire_disabled:
```
  def edit_forhire
    patch forhire_path(@forhire1),
          params: { forhire: { blurb: 'I stopped Largo twice!' } }
  end

  def edit_forhire_disabled
    edit_forhire
    assert_redirected_to root_path
  end
```
* Edit the file test/controllers/forhires_controller_test.rb.  Add the following code just before the last "end" statement:
```
  test 'should redirect edit to root when not logged in' do
    edit_forhire_disabled
  end

  test 'should not redirect edit when logged in as the user' do
    sign_in @u1, scope: :user
    edit_forhire
  end


  test 'should redirect edit to root when logged in as a different user' do
    sign_in @u2, scope: :user
    edit_forhire_disabled
  end

  test 'should redirect edit to root when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    edit_forhire_disabled
  end

  test 'should redirect edit to show when logged in as a super admin' do
    sign_in @a1, scope: :admin
    edit_forhire
    assert_redirected_to forhire_path(@forhire1)
    assert page.has_text?('For hire updated')
  end
```

### Part B: View Level

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added the ability to edit forhires at the view level"
git push origin 04-06-forhire_edit
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".
