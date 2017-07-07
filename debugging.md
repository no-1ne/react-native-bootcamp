---
layout: default
title: "Debugging"
---
## __Debugging__

For most parts of it React Native error aren't descriptive at all, so you have to try and test a few techniques to get it to work. here are a few thing's you can try

### _noVNC error 404_
- Sometimes we might see a 404 Error when we run the emulator, this usually happened when the `vnc_auto.html` file was changed to `vnc_lite.html`. to fix this, in your `android-emulator:run` change your __Preview URL__ from `http://${server.port.6080}` to `http://${server.port.6080}/vnc_lite.html`


### _Console Log_  
- We can use console.log for debugging, remember we created a command with the name `adb-logcat:run` try running it after starting the emulator. And you'll see that all the logs from our Emulator are getting logged here. There are three types of logs 
	- console.log
	- console.error
	- console.warn  
* console.error will show you a red screen on the emulator when called
* console.warn will show you warning at the bottom of the screen
* console.log won't show up on the emulator but would be logged in the `adb logcat`  


Sometimes when you are testing the app you might get a red screen with an error, and most of the times these errors are vague. So here are a few things you can do to make it work.
### _Stack trace_  
You can sometimes find where the error is just by looking at the error screen, in that case go back to your code and make the required changes.
	- Even if at the top you don't see an error in a file that's not familiar go down to see if you can find a file that you know of.
### _./gradlew clean_  
This command cleans your android project. run it after going into your android. here are the steps,
	- from the TravelLog directory run `cd android`
	- run `./gradlew clean`
	- run `react-native run-android` from `TravelLog` directory 


### _Re-install_
As a last measure you can
- uninstall the app from the emulator
- stop packager server 
- restart the packager server
- run your app with `react-native run-android` 


### _Some common errors and fixes_  
here are some common error that you might encounter and how to fix them,

* <span style="color:red">Can't find variable: [yadayada]  <span>  

	This is a common error and usually means that you forgot to import or initialize a function, method or a variable. just go to the line and verify  
* <span style="color:red">You have not accepted the license agreements of the following SDK components:  <span>  
	
	This another creepy worm that we were prepared for but still slips out of our shoe, so here's how to fix this issue.  
	* copy this line : <code class="highlighter-rouge">android update sdk --no-ui --all --filter "<span style='color:orange'>replace_with_required_sdk_component</span>"</code>
	* paste it in the codenvy workspace
	* change the `replace_with_required_sdk_component` to your component that requires license acceptance.
	* press enter and then enter `y` when it prompts to accepts the license.  
	You are good to go!  
* <span style="color:red">You cannot render into anything but a top root"  <span>

### _Workspace stops while build and prompts for restart again and again_  
This issue is specific to [codenvy](https://codenvy.io/dashboard/) and will start showing up as your project gets big, we encountered this error on [part9] 

If nothing of the above work May God save you😜, Just kidding You can ask any of the mentors to help you debug anytime. Just make sure you first follow the above steps to save time.