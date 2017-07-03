---
layout: page
title:  Codenvy workspace and Nodejs
category: part1
---

### _create codenvy workspace_
Since we have already worked with Codenvy we won't be going into details. Almost all steps of starting workspace are same except one.
- Go to [codenvy.io](http://codenvy.io/dashboard/) dashboard, login if you haven't already.
- Click on [Create Workspace](https://codenvy.io/dashboard/#/create-project).
  1. Select Source : New From Blank
  2. Select Workspace : Create new workspace from stack
    - Android (<span style="color:red">!Important</span>)
  3. Configure workspace : 
    - Name : Change as you please, or leave default
    - RAM : 3 Gigabytes (<span style="color:red">!Important</span>)
  4. Template : Leave Default(we'll delete it anyway)
  5. Project Metadata : Leave Default
  6. Click Create
- Now You have a Codenvy workspace ready with 3 GB memory and Android SDK Installed

### _Update Android SDK and install Nodejs_
To be able to use Android SDK, you must first accept the Android SDK License. To be able to accept the license from the CLI you must update each SDK you are using individually. the command to update Android SDK is `android update sdk [--other_options]`. You'd also need [NodeJs](https://nodejs.org/)(Duh!), In your last bootcamp Node was pre install but in this one you have to install it manually. To save you the hassle we have put those two commands into single sh file, now all you have to do is get that sh file and run it. here's the command for that,<center><strong><code>wget https://raw.githubusercontent.com/pavitran/sand_box/master/rn.sh && sh rn.sh</code></strong></center>

### _Configure Commands_
Codenvy let's us define commands so that we can run them with just a click. In your workspace you must currently be in Project Explorer, delete the default project and switch to Commands Explorer by clicking on __Commands__,
  * from here under run there's a default command, double click on it to edit
  * change the name of command to `android-emulator:run`
  * In Command Line remove the default command and add `echo "android emulator on port.."`
  * leave the __Preview URL__ option unchanged(why? will explain later)
  * save and run  
  
Add another command by clicking "+" icon beside RUN  
  * double click on `custom`, this will give you a new command
  * rename it to `packager-server:run`
  * add command `cd /projects/TravelLog/ && npm start`
  * save (don't run yet)  

Add another command by clicking "+" icon beside RUN  
  * double click on `custom`, this will give you a new command
  * rename it to `adb-logcat:run`
  * add command `adb logcat`
  * save (RUN it after the emulator is running) 

### _Android Emulator_
In the Last Section when you save and run it gives you a link, click on it and it will bring you to a blank gray screen. Right click on it and select __Emulator__. give it some time and *Voila!* you have your very own mobile device to play with and install apps on, BTW it's called an __Emulator__. Now a word of caution, this little thing is very delicate and you must handle it with care 
  * don't click the buttons again and again if it's not responding
  * don't click the reload button in the browser
  * don't press unnecessary button
  * don't let it shut down, it takes time to reboot
  * be gentle!  

### _Install React Native CLI_
For those curious guys out there CLI stands for Command Line Interface(If you didn't know that already). React Native CLI is a npm package that is required and must be installed globally to create a React native App. To install react-native-cli run <center><strong><code>sudo npm install -g react-native-cli</code></strong></center> from anywhere, this will install react-native-cli globally and you are ready to create your first React native Project.

### _Create React Native Project_
You just need to remember one command to be able initialize a React Native Project, the command is `react-native init <project_name>`. That's it that's all you need to do, this will create a project with all the necessary node modules, an android project and an IOS project(Although we'll be concentrating on Android part only). Now let's create a new project,

<center><strong><code>react-native init TravelLog</code></strong></center>
  
You can name your project anything, but for consistency's sake keep it as __TravelLog__ for Now.  

### _Project Structure_
If you followed all the above steps properly you should now have a basic React Native Project in your projects directory named TravelLog. with the following project structure
```
TravelLog
└─── __tests__
│      
└─── android
│
└─── ios
│   
└─── node_modules
│       
└─── app.json
│       
└─── index.android.js
│       
└─── index.ios.js
│       
└─── package.json
```
Ignoring the \_\_tests\_\_, let me briefly explain what all these files and folders do.

  * __android__ : this is where all the android project resides
  * __ios__ : this is where all the ios project resides(duh!)
  * __node_modules__ : this directory contains all the modules required by the project, including `react-native`. all the external modules we install will also be here.
  * __app.json__ : just contains apps name and display name
  * __index.android.js__ : this is the entry point for android app
  * __package.json__ : serves as a registry to locally installed npm packages. 

### _Run The Project_
To run the project:  

  * change into the project directory `cd TravelLog`
  * from the commands created earlier run `packager-server:run`(why? will explain later)
  * from the terminal run `react-native run-android`
  * if the above command ran successfully you should see a "Welcome to React Native" on your emulator(fancy name for mobile you are seeing)

Congratulations, now you have successfully created and run a basic React Native App, That's it for Part I. In the [next part](part2/home-screen.html) we'll start playing with the code, make UI changes and more...
<center>Happy Coding!</center> 
<center>---***---</center>
***

**Note:**

- Codenvy shuts down your workspace after every 10 minutes of inactivity, and in this bootcamp it's very important to respect that point. Android emulator take time to load!
- if you get a gray screen in the novnc link, just right click and select __Emulator__.
- Emulator must be running to run `react-native run-android` otherwise It'll give a "no android devices running" error.
- If you get stuck somewhere just checkout the [debugging] Section
