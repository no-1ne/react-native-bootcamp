---
layout: page
title:  Authentication
category: part9
---

If you didn't notice yet, We are writing to the database without any authentication(never ever do that). So in this part we'll be implementing the Login Screen and make adding to the database with authentication mandatory. As I said before Authentication on React Native with firebase isn't straight forward as on ReactJS(That's the reason we are implementing it now). So for ease's sake we'll implementing Login with only Facebook, Google Login should also follow the same flow but since it's a different platform a few steps gonna change.  
So let's list out pieces that'll go into implementing Facebook auth. with firebase,  
- install react facebook sdk
- create a Facebook app 
- integrate Facebook login
- integrate firebase auth with Facebook  

now these might seem like very few and short steps. but trust me, they aren't!

### _Install react facebook sdk_  
* `npm install react-native-fbsdk@0.6.0 --save`  
* `react-native link react-native-fbsdk`  

### _Create Facebook App_  
To implement Facebook login we have to create a Facebook App, these are the steps needed to create a Facebook app.
* Go to [Facebook Developers Console](https://developers.facebook.com/)
	- If you aren't logged in Login
	- Go to my `My Apps` at the top right
	- Click on `Add A New App`
		* Give your app a Display name in our case `Travel Log`
		* Click `Create App ID`
		* Complete captcha (If prompted)
		* Hit `Submit`
* In your dashboard you should see a `APP ID` in the header, take note of it.
* Make app public
	- In the Left Navigation Click `App Review`
	- Now there will be an option saying `Make Travel Log public?`(if you named your app Travel Log)  
		* switch the radio button to yes
		* Choose a category in our case Travel
		* Hit `confirm`  
Don't forget to make app public (<span style="color:red">!Important</span>) 


### _Integrate Facebook login_     
Integrating Facebook Login is a bit tricky, make sure you've completed the install and link part successfully before continuing to this part,  
__note : don't ask me why we are implementing these steps, because that's how the facebook documentation tells us to implement it__  

* To integrate with Android we have to change exactly five android files they are,
	1. MainApplication.java (/android/app/src/main/java/MainApplication.java)
	2. MainActivity.java (/android/app/src/main/java/MainActivity.java) 
	3. AndroidManifest.xml (/android/app/src/main/AndroidManifest.xml)
	4. strings.xml (/android/app/src/main/res/values/strings.xml)  
	5. build.gradle (/android/app/src/build.gradle && node_modules/react-native-fbsdk/android/build.gradle)
* MainApplication.java
	- add following lines at the top of the file 
	```
	import com.facebook.CallbackManager;
	import com.facebook.FacebookSdk;
	import com.facebook.reactnative.androidsdk.FBSDKPackage;
	import com.facebook.appevents.AppEventsLogger;
	```  
	- add the following lines inside MainApplication Class, at the top  
	```
	  private static CallbackManager mCallbackManager = CallbackManager.Factory.create();

	  protected static CallbackManager getCallbackManager() {
	    return mCallbackManager;
	  }
	``` 
	- add the following line just after `super.onCreate();`, inside onCreate() method in MainApplication Class
	```
	FacebookSdk.sdkInitialize(getApplicationContext());
	```  
	- change your getpackages() method as follows (<span style="color:red">!Only change FBSDK part</span>)
	```
		@Override
    protected List<ReactPackage> getPackages() {
		  return Arrays.<ReactPackage>asList(
		      new MainReactPackage(),
		        new FBSDKPackage(mCallbackManager),
		        new RNFetchBlobPackage(),
		        new MapsPackage(),
		        new VectorIconsPackage(),
		        new RCTCameraPackage()
		  );
    }
	```  

* MainActivity.java
	- add the following import statement to you MainActivity.java  
		`import android.content.Intent;`  
	- add the following override method inside Your Class
	```
	@Override
	public void onActivityResult(int requestCode, int resultCode, Intent data) {  
	    super.onActivityResult(requestCode, resultCode, data);  
	    MainApplication.getCallbackManager().onActivityResult(requestCode, resultCode, data);  
	}  
	```  

* AndroidManifest.xml  
	- in AndroidManifest there'll be a `application` element with a few `activity` elements, add these two `activity` elements to the `application`  
		<pre><code class="xml">   
		&lt;activity android:name="com.facebook.FacebookActivity"
		    android:configChanges=
		            "keyboard|keyboardHidden|screenLayout|screenSize|orientation"
		    android:label="@string/app_name" /&gt;
		&lt;activity
		    android:name="com.facebook.CustomTabActivity"
		    android:exported="true"&gt;
		    &lt;intent-filter&gt;
		        &lt;action android:name="android.intent.action.VIEW" /&gt;
		        &lt;category android:name="android.intent.category.DEFAULT" /&gt;
		        &lt;category android:name="android.intent.category.BROWSABLE" /&gt;
		        &lt;data android:scheme="@string/fb_login_protocol_scheme" /&gt;
		    &lt;/intent-filter&gt;
		  &lt;/activity&gt;
		</code></pre>  
	- add the following meta tag just after the activity
		```
		<meta-data android:name="com.facebook.sdk.ApplicationId" android:value="@string/facebook_app_id"/>
		```  
* strings.xml
Notice that `@string/facebook_app_id` in the above meta tag, we actually have to put an element in `strings.xml` file with the name `facebook_app_id`.  
	add these two lines of code to your strings.xml, and change the `[ADD YOUR APP ID]` to your App ID, don't leave any space in between in the second tag.
	<pre>
	&lt;string name="facebook_app_id"&gt;<span style="color:orange">[ADD YOUR APP ID]</span>&lt;/string&gt;
	&lt;string name="fb_login_protocol_scheme"&gt;fb<span style="color:orange">[ADD YOUR APP ID]</span>&lt;/string&gt;
	</pre>  
* build.gradle
	- There are two `build.gradle` files that we have to update in order to make it work, one is in the 
	android project (/android/app/src/build.gradle). add or change the following facebook sdk version inside `dependencies`  
	`compile 'com.facebook.android:facebook-android-sdk:[4.22.1)'`  
	- In __node_modules->react-native-fbsdk->android->build.gradle__ change or add following inside `dependencies`  
	`compile('com.facebook.android:facebook-android-sdk:4.22.1')`

And with that you are done integrating Fbsdk in your project. Let's add a login Screen and change Form Screen so that it makes the user Login before adding to the database.  

### _Login Screen_  
Let's create a basic class that extends a `Component`, name it LoginScreen and add it to the file `LoginScreen.js`, Don't forget to have a empty render command in your Class(since you have created many component by now, you must have pretty good idea on how to create a basic Component). In our login Screen we'll just have a button that when clicked logs the user in and takes back to the Form Screen. So let's start building it, the first thing we have to do is import FBSDK and the two functions from it. here are the commands
* `import FBSDK from 'react-native-fbsdk';`
* `import * as firebase from 'firebase';`
* `const { LoginButton, AccessToken} = FBSDK;` 

Now add the following code to your render return function,  

```
<View style={styles.container}>
        <LoginButton
          onLoginFinished={
            (error, result) => {
              if (error) {
                alert("login has error: " + result.error);
              } else if (result.isCancelled) {
                alert("login is cancelled.");
              } else {
                AccessToken.getCurrentAccessToken().then(
                  (data) => {
                    const provider = firebase.auth.FacebookAuthProvider;
                    const credential = provider.credential(data.accessToken.toString());
                    firebase.auth().signInAndRetrieveDataWithCredential(credential)
                    .then(function(userCredential) {
                      this.props.navigation.goBack();
                    });
                  }
                )
              }
            }
          }
          onLogoutFinished={() => alert("logout.")}/>
      </View>
```  
Let's break this code into pieces, first of all we have a View Component with the `style` prop set to `styles.container`(Don't worry we'll add it in next step). Inside the View we have LoginButton Component which takes two props `onLoginFinished` and `onLogoutFinished` which takes functions and are called when the user Logs in and Logs out(duh!). When the user successfully logs in we are authenticating the user with Firebase using the Token from FBSDK Login. on successful Login we are taking the user back to the Form Screen with `this.props.navigation.goBack();`. Let's define the style that are used by the View component, all this style object does is center the Login button on the screen,  
```
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#F5FCFF',
    alignItems : 'center',
  },
});
```  
__note: don't forget to add you Login Screen to the stack navigator__  

With that our Login Screen is done!

Since now we have added auth. let's change our Form Screen and Main Screen to write and read moments of each user separately.  
#### _Form Screen_  
* import firebase `import * as firebase from 'firebase';`  
* In component did mount add the following function which triggers when auth state changes  
	`firebase.auth().onAuthStateChanged(this.handleAuthChange);`  
* in the above code firebase auth calls `handleAuthChange` which doesn't exist add the method and also don't forget to bind it in the constructor,  
```
  handleAuthChange(user){
    if (user !== null) {
      this.setState({user_id : user.uid});
    } else {
      this.props.navigation.navigate('Login');
    }
  }
```  
In the following method we check if the user is logged in and than if he is we set the `user_id` state to current user id. otherwise we navigate the use to the Login Screen.  
* Change the db update inside `addData()` method as follows, don't blame me if you get `can't find navigate`  
	`this.databaseRef.child(""+this.state.user_id).update(put_object).then(navigate('Main')).catch((error) => alert(error));`  

#### _Main Screen_
Let's update our Main Method to get moments of the user,  
* change the `constructor` method to get the user id, with firebase auth change
`firebase.auth().onAuthStateChanged(this.handleAuthChange);`  
* Add the method `handleAuthChange` to MainScreen
```
handleAuthChange(user){
    if (user !== null) {
      this.setState({user_id : user.uid});
      console.log("User logged in");
      this.itemsRef = firebase.database().ref(user.uid);
      this.listenForItems(this.itemsRef);
    } else {
      this.setState({logged_in : false});
    Alert.alert(
      'Alert',
      'You are not logged in please login to see your moments',
      [
        {text: 'Login', onPress: () => this.props.navigation.navigate('Login')},
      ],
      { cancelable: true }
    );
    }
  }
```  
We have moved our database reference call and `listenForItems` call into `handleAuthChange` now let's bind the above method  
`this.handleAuthChange = this.handleAuthChange.bind(this);`  
Now with that we successfully Authenticated ourselves into the [Wrap Up]({{ site.url }}{{site.baseurl}}/part10/wrap-up.html)...




