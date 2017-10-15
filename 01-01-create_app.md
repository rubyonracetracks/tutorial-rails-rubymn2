# Unit 1
## Chapter 1: Creating the App

* Reset the rbenv-general Docker image.
* In your Docker container, install the GenericApp gem by entering the command "gem install generic_app".
* Enter the following commands:
```
DATE=`date +%Y%m%d-%H%M%S-%3N`
APP_NAME=rubymn-$DATE
echo $APP_NAME
```
* Enter the command "generic_app".  When prompted for the name of your new app's directory, copy and paste the output of the above echo command.  When prompted for the name of your new app, enter "Ruby Users of Minnesota".
* Follow the instructions in the README-to_do.txt file.  After you have completed these tasks, you are ready to move on.
