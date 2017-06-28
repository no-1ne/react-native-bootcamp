---
layout: page
title:  React Native Camera
category: part7
---

### _Intro_
The Main functionality of this App is being able to Capture a moment, adding a Title and saving it to Firebase with the Geo Location. And we having accomplished any of these yet. So Let's Implement A Camera Screen that take a Picture. There is a [React Native Camera](https://github.com/lwansbrough/react-native-camera) module, which would do the job for us. To Get started install and link the module with following commands..
* `npm i react-native-camera@0.6 --save`
* `react-native link react-native-camera`  

Remember We edited AndroidManifest.xml to add a Google API. In this Part also we are gonna edit the AndroidManifest.xml(/android/app/src/main/AndroidManifest.xml) again but this time we'll be adding permissions instead of meta data, these permissions would let us Read and Write From our Local Storage. Since our Camera Component would be Saving file to local storage these permissions are necessary, add the following lines of code along with other permissions
```
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" /> 
```  
also add `android:largeHeap="true"` just after `android:allowBackup="true"`

Now 
With this we are ready to Implement our Camera Screen.
### _Camera Screen_
Our Camera Screen will consists of a Preview view where we can see what we are shoot, and a Icon button to take the Picture. So let's get started...
* Start By creating a file `CameraScreen.js` inside `components` directory
* make the necessary React native import, additionally
	- `import Camera from 'react-native-camera';`  
	- `import { Icon } from 'react-native-elements';`
* add the default Component code with a View and a Camera Component inside it. 
	```
export default class CameraScreen extends Component {
  render() {
    return (
    <View style={styles.container}>
        <Camera
			ref={(cam) => {
				this.camera = cam;
			}}
			style={styles.preview}
			aspect={Camera.constants.Aspect.fill}
			orientation={Camera.constants.Orientation.landscapeRight}>
          
			<Icon raised name='camera' type='font-awesome' color='#f50' onPress={this.takePicture.bind(this)} />
		</Camera>
	</View>
    );
  }
}
	```  
	Let's see what we are doing here, we have added a View whose style is set to style.container(will define it in next step). We have a Camera with with aspect ration is set to FIll and orientation to Landscape. We've added an Icon which on press calls a method named takePicture(not defined yet)  

* Defining the styles constant  
	```
	const styles = StyleSheet.create({
		container: {
			flex: 1,
			backgroundColor: '#F5FCFF',
		},
		preview: {
			flex: 1,
			justifyContent: 'flex-end',
			alignItems: 'center'
		},
	});
	```
	these are styles used by our Camera and View Component.
* Now we'll define `takePicture` that is called when the Icon is clicked  
	```
takePicture() {
    this.camera.capture()
      .then((data) => {
        console.log(data.path);
        this.setState({moment_time : Date.now().toString(), image_path : data.path});
        this.props.navigation.navigate('Form', { moment_details : this.state});
      })
      .catch(err => console.error(err));
  }
	```
	this method invokes the camera.capture() function and if successful logs the image path and set the state to image_path and the time.  
	And with that we have beautifully captured Part 7, Let's shoot to [Part 8]({{ site.url }}{{site.baseurl}}/part8/react-native-blob-and-firebase-upload.html)
