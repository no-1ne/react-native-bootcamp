---
layout: page
title:  React Native Maps
date:   2013-06-03 21:12:57
category: part6
order: 0
---

### _Intro_
In DetailScreen we have a Map View in the background where we show the location of the moment. To implement that we will use the [react-native-maps](https://github.com/airbnb/react-native-maps){:target="_blank"} by [airbnb](https://www.airbnb.com/){:target="_blank"}. To get started with `react-native-maps` you must run the following commands to install and link the package.
* `npm install react-native-maps --save`
* `react-native link react-native-maps`  

### _Google Maps API Key_
To Use Google Maps in your App you must have Google Maps API key, Go get the API key by following these steps:
* You must use [Google Developer Console](https://console.developers.google.com/){:target="_blank"} to get the API Key
* Create a new Project by going to [create project](https://console.developers.google.com/projectcreate){:target="_blank"}
	- Give your project a name
	- accept the terms 
	- Click on "Create"
* Click on Hamburger Icon from top left and select [API Manager](https://console.developers.google.com/apis)
* Select [Google Maps Android API](https://console.developers.google.com/apis/api/maps_android_backend/overview){:target="_blank"} from the API List and click "ENABLE".(<span style="color:red">!Important</span>)
* In Left Nav. Select "Credentials"
	- Click "Create credentials"
	- Select API Key
	- Copy API Key from the popup dialog.  

### _Adding API Key to Android Project_
For your android app to work, you must add the API key you got from last step to your Android project. The whole point of React Native is to touch android as less as possible. So we'll stick to that rule, and won't dig into android project much.  
To add the API key,  
* open /android/app/src/main/AndroidManifest.xml  
* add the follow code to the above file just before closing `</application>` Tag. And replace `your_api_key` with you guessed it right, your API key😜
	<pre>
	&lt;meta-data
      android:name="com.google.android.geo.API_KEY"
      android:value="<span style="color:orange">your_api_key</span>"/&gt;
	</pre>  
That's it that's all you need to do to use Google Maps API With React Native Maps. In the next Step We will implement the Map View in DetailScreen.

## _Detail Screen_
In [Detail Screen]({{ site.url }}{{site.baseurl}}/assets/detail-screen.png) we already implemented the Carousel component with all the moments in it, Now we'll add the Map Component in the background with following steps,
* In `DetailScreen.js` import `MapView` from `react-native-maps` with following command  
	`import MapView from 'react-native-maps';`
* Remember we had a View component with Text saying `Your Map Location Here`. well now is the time to put "Your Map Location Here"😉. Remove the  Text component, and add MapView there as follows  
	```
	<MapView style={styles.map}
		initialRegion={{ "{{latitude: this.state.location.lat,
		longitude: this.state.location.lon,
		latitudeDelta: 0.0922,
		longitudeDelta: 0.0421," }}}}
	>
		<MapView.Marker 
			coordinate={{ "{{latitude: this.state.location.lat,longitude: this.state.location.lon" }}}}
		/>
	</MapView>
	```
	This will add a map with intialRegion set to our latitude and longitude. latitudeDelta,longitudeDelta defines the zoom of the map. The MapView.Marker puts a marker on the Map at given lat. and lon. Hold On! Do we have the lat. and lon. assigned to location state? I don't remember doing anything like that. Let's do that now...
	```
	var details = this.props.navigation.state.params.details;
	this.state = {
        location : {
        lat : details.location.lat,
        lon : details.location.lon
      }
      }
	```
* In the above MapView we have style prop set to map object that we don't yet have in our styles object , these styles are required to display the Map. Add the following object to our styles object.(don't curse me if you forgot the comma😌)
	```
  map: {
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
  }
	```  
__Note:__ Our emulator doesn't support Google Play services so we won't be able to see the map(yet). Don't worry we'll see the map soon, just make sure you followed all the above steps properly and you get `won't run without google play services which are not supported by your device` in you `DisplayScreen`.  

### _Aren't done yet!_
But wait! we aren't done yet, are we? We have successfully displayed the location of the moment we got from the main screen. But Slides in our Carousel keep changing and it would be stupid to show the same location even though our moments are changing. So, we add a feature where our map animates to the new location and moves the marker. Now how cool is that😎!  
* Who remembers the important attribute of [Carousel]({{ site.url }}{{site.baseurl}}/part4/detail-screen.html#carousel) that I talked about in [Detail Screen]({{ site.url }}{{site.baseurl}}/part4/detail-screen.html)?🤚  
Well the name of that attribute is `onSnapToItem`, and if you go back and see, we are printing the location to console every time we snap To a new Slide. Ok, But why is that important? Because that's where all the magic gonna happen. Here's the plan...  
	* We'll get the location of the current moment using the index that we got from the `onSnapToItem`.
	* Write a method that takes location object as the parameter and animates the map to the location.
	* Change the location state in `onSnapToItem` to the current moment location
	* Call the method from the `onSnapToItem` with the location, and see the magic✨.  
* To animate MapView region dynamically we need the reference to the MapView, To get the reference of the MapView add the following attribute to MapView.  
	`ref={(ref) => { this.mapRef = ref }} `  
	now we can reference the MapView with `this.mapRef`  
* Now let's add a method that when called animates the Map to the given Location.  
	```
	centerMapOnMarker (momentLocation) {
        if (!momentLocation) {
            return;
        }
        try{
          this.mapRef.animateToRegion({
              latitude: momentLocation.lat,
              longitude: momentLocation.lon,
              latitudeDelta: 0.0315,
              longitudeDelta: 0.0258
          });
        }catch(e){
          console.log(e);
        }
    }
	```  
	This Method when called, calls the `animateToRegion` method on the MapView reference. We wrap it inside a try/catch just to be safe. If and if not Moment Location is given it'll just return nothing.(don't forget to bind this method)  
* Since this method is done, let's call it from the `onSnapToItem` callback. We are logging the location inside `onSnapToItem` callback, now remove the `console.log` and replace it with a `setState` and a `centerMapOnMarker` Method call. Now your `onSnapToItem` should look like..
	```
	onSnapToItem={(index) => {
		var location = this.props.navigation.state.params.details_list[index].location;
		this.setState({location : location});
		this.centerMapOnMarker(location);
	}}
	```


With this we knocked out map out of our map. Navigating to [Part VII]({{ site.url }}{{site.baseurl}}/part7/react-native-camera-and-blob.html)...
