---
layout: page
title:  React Native Maps
date:   2013-06-03 21:12:57
category: part6
order: 0
---

### _Intro_
In DetailScreen we have a Map View in which we show the location of the moment. To implement that we will Use the [react-native-maps](https://github.com/airbnb/react-native-maps) by [airbnb](https://www.airbnb.com/). To get started with `react-native-maps` you must run the following commands to install and link the package.
* `npm install react-native-maps --save`
* `react-native link react-native-maps`  

### _Google Maps API Key_
To Use Google Maps in your App you must have Google Maps API key, Go get the API key by following these steps:
* You must use [Google Developer Console](https://console.developers.google.com/) to get the API Key
* Create a new Project by going to [create project](https://console.developers.google.com/projectcreate)
	- Give your project a name
	- accept the terms 
	- Click on "Create"
* Click on Hamburger Icon from top left and select [API Manager](https://console.developers.google.com/apis)
* Select [Google Maps Android API](https://console.developers.google.com/apis/api/maps_android_backend/overview) from the API List and click "ENABLE".(<span style="color:red">!Important</span>)
* In Left Nav. Select "Credentials"
	- Click "Create credentials"
	- Select API Key
	- Copy API Key from the popup dialog.  

### _Adding API Key to Android Project_
For your android app to work, you must add the API key you got from last step to your Android project. The whole point of React Native is to touch android as less as possible. So we'll stick to that rule, and won't dig into android project much.  
To add the API key,  
* open /android/app/src/main/AndroidManifest.xml  
* add the follow code to the above file just before closing `</application>` Tag. And replace `your_api_key` with you guessed it right, your API key😜
	```
	<meta-data
      android:name="com.google.android.geo.API_KEY"
      android:value="your_api_key"/>
	```  
That's it that's all you need to do to use Google Maps API With React Native Maps. In the next Step We will implement the Map View in DetailScreen.

## _Detail Screen_
in [Detail Screen]({{ site.url }}{{site.baseurl}}/assets/detail-screen.png) we already implemented the first row with Tile Component, Now we'll add the Map Component in the second row with follow in steps,
* In `DetailScreen.js` import `MapView` from `react-native-maps` with following command  
	`import MapView from 'react-native-maps';`
* Add A View Component to the Second Row, and add MapView inside it as follows  
	```
	<View style={styles.container}>
		<MapView style={styles.map}
			initialRegion={{ "{{latitude: details.location.lat,
			longitude: details.location.lon,
			latitudeDelta: 0.0922,
			longitudeDelta: 0.0421," }}}}
		>
			<MapView.Marker 
				coordinate={{ "{{latitude: details.location.lat,longitude: details.location.lon" }}}}
			/>
		</MapView>
	</View>
	```
	This will add a map with intialRegion set to our latitude and longitude. latitudeDelta,longitudeDelta defines the zoom of the map. The MapView.Marker puts a marker on the Map at given lat. and lon.
* In the above View we set the style to styles object that we didn't define yet, these styles are required to display the Map.  
	```
const styles = StyleSheet.create({
  container: {
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
    justifyContent: 'flex-end',
    alignItems: 'center',
     backgroundColor : "#ff0000"
  },
  map: {
    position: 'absolute',
    top: 0,
    left: 0,
    right: 0,
    bottom: 0,
  },
});
	```  
__Note:__ Our emulator doesn't support Google Play services so we won't be able to see the map(yet)s. Don't worry we'll see the map soon, just make sure you followed all the above steps properly and you get `won't run without google play services which are not supported by your device` in you `DisplayScreen`.

With this we knocked out map out of our map. Navigating to [Part VII]({{ site.url }}{{site.baseurl}}/part7/react-native-camera-and-blob.html)...
