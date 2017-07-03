---
layout: page
title:  Form Screen
category: part8
---

So we have implemented a Main Screen but we aren't displaying any data of our own to it, are we? So let's add a Form Screen which let's us upload an Image, and add some data related to the moment into the Database. So Let's Get Started

### _Firebase Upload_
In our Last Bootcamp we already learned how to upload images to [Firebase Storage](https://firebase.google.com/docs/storage/). In React Native Firebase file upload won't be that straight forward. To Upload to firebase we must first create blob from file URI, then we can upload the blob to firebase with `firebase.storage().ref(),put()`. So How do we create a Blob from a image URI? There is React Native module with the name [react-native-fetch-blob](https://github.com/wkh237/react-native-fetch-blob). this module given a resource URI converts it into a blob. Let's install 
[react-native-fetch-blob](https://github.com/wkh237/react-native-fetch-blob). run these two commands to install and link react-native-fetch-blob  
* `npm install --save react-native-fetch-blob`
* `react-native link react-native-fetch-blob`  
 
With React Native Fetch Blob installed we can now start working on our Form Screen  

### _Form Screen_  
* If you go back to our wireframe design, When we click a photo in Camera Screen it should take us to the Form Screen where we can Upload data to firebase. Let's create a new Screen with the name `FormScreen.js` inside components directory.
* Our Form Screen should contain following components
	- An input field for the moment title.
	- A Image preview to let us preview photo from the Camera Screen
	- A Button which when click should upload the image to firebase and add data to our firebase database.
* In Form Screen add a View which should contain an Input Field with a Label. So your render method should look like..
```
    return (
      <View style={styles.container}>
        <FormLabel labelStyle={styles.labelFont}>Moment Title</FormLabel>
        <FormInput onChangeText={(data)=>console.log(data)} />
      </View>
    );
```
* define the style constant
```
const styles = StyleSheet.create({
  labelFont : {
    textAlign: 'center',
    fontSize: 20,
    fontWeight: 'bold',
  },
  container: {
    flex: 1,
    justifyContent: 'center',
    backgroundColor: '#F5FCFF',
  },
});
```
* Now let's add a preview of our image and a button to add the moment to our database.
```
    return (
      <View style={styles.container}>
        <FormLabel labelStyle={styles.labelFont}>Moment Title</FormLabel>
        <FormInput onChangeText={(data)=>this.setState({title : data})} />
        <FormLabel labelStyle={styles.labelFont} >Moment Image</FormLabel>
          <Image
            resizeMode='contain'
            source={{ "{{uri: this.props.navigation.state.params.moment_details.image_path" }}}}
            style={{ "{{height: 100, width: null, margin: 15" }}}} />
        <Button
          title='CREATE MOMENT' />
      </View>
    );
```
	- hey! where are getting this `this.props.navigation.state.params.moment_details.image_path` from?  
	oops! you got me, this actually is supposed to be the URI of the photo we took in the Camera Screen noticed `this.props.navigation.state.params`? that means we are getting this from navigation prop and some other Screen is sending this data. So let's go back to Camera Screen.
* Remember we implemented takePicture method in Camera Screen, the code inside should return a image URI. But because of the codenvy limitations we are going to fake it for now, add the following after the commented code inside `takePicture` in `CameraScreen.js`
	- ```
	this.props.navigation.navigate('Form', { moment_details : {moment_time : Date.now().toString(), image_path : "http://d33y93cfm0wb4z.cloudfront.net/Ella/client_content/banana_boat/beach_games.jpg"}});
	``` 
	What this line does is, when the takePicture method is invoked(it's invoked when we click on camera Icon) it navigates us to Form Screen with some additional data that we can use on the other side.
* We didn't actually add the Form Screen to our Stacknavigator did we? let's do that 
	- import `FormScreen.js` in `index.android.js`  
		`import FormScreen from './components/FormScreen.js';`  
	- add Form Screen to Stacknavigator  
		`Form : {screen : FormScreen},`
* Now when we click on the Camera Icon we are taken to the Form Screen, where we get a image preview, a Input Field and a Button. But wait this button doesn't do anything magical, does it? let's sprinkle some stardust on this button and make it put image to storage and add data to our database.  
	* #### _React Native Fetch Blob_  
	As I said before we need [React Native Fetch Blob](https://github.com/wkh237/react-native-fetch-blob) to be able to upload images to firebase. to be able to use it have to do an import and a bunch of initializations. add the following lines after the import statements...  
	```
import RNFetchBlob from 'react-native-fetch-blob';
const Blob = RNFetchBlob.polyfill.Blob;
const fs = RNFetchBlob.fs;
window.XMLHttpRequest = RNFetchBlob.polyfill.XMLHttpRequest;
window.Blob = Blob;
	```
	With this done let's create a method `uploadImage` that takes three arguments a name, a file uri(in our case an image) and an optional mime argument. here's the method..
	```
  uploadImage(moment_time,uri, mime = 'application/octet-stream') {
    return new Promise((resolve, reject) => {
      //const uploadUri = uri;
      const uploadUri = Platform.OS === 'ios' ? uri.replace('file://', '') : uri;
      let uploadBlob = null;

      const imageRef = firebase.storage().ref('images').child( moment_time + '.jpg');
      console.log(imageRef);
      fs.readFile(uploadUri, 'base64')
        .then((data) => {
          return Blob.build(data, { type: `${mime};BASE64` });
        })
        .then((blob) => {
          uploadBlob = blob;
          return imageRef.put(blob, { contentType: mime });
        })
        .then(() => {
          uploadBlob.close();
          //do stuff here
          return imageRef.getDownloadURL();
        })
        .then((url) => {
          resolve(url);
        })
        .catch((error) => {
          reject(error);
      });
    });
  }
	```  
	So what this method does is given a file URI and a name it read the file and return a Blob created from it, then the blob is out to the firebase with firebase.storage().ref().put() function. then it closes the blob and returns the downloadURl from firebase.
* Also we need to get the Geo Location for the maps, in react-native getting the location is straight forward. all we have to do is use `navigator.geolocation` same as we do in the web., let's get the current location of the user in the `componentDidMount()` of `FormScreen.js`. So now should have a `componentDidMount()` that looks something like this..
```
componentDidMount(){
      navigator.geolocation.getCurrentPosition(
         (position) => {
            this.setState({ lat : position.coords.latitude, lon : position.coords.longitude });
         },
         (error) => alert(error.message),
         { enableHighAccuracy: true, timeout: 20000, maximumAge: 1000 }
      );
  }
```
* Now let's add a method that gets called when our Button is pressed  
	```
	addData(){
		this.uploadImage(this.props.navigation.state.params.moment_details.moment_time,this.props.navigation.state.params.moment_details.image_path);
		const moment_details = this.props.navigation.state.params.moment_details;
		this.databaseRef = firebase.database();
		var put_object = {
    time : moment_details.moment_time,
		title : this.state.title,
		image : "/images/" + moment_details.moment_time + ".jpg",
		location : {
			lat : this.state.lat,
  			lon : this.state.lon
		}
		};
		this.databaseRef.update(put_object).then(alert("success")).catch(alert(fail));
	}
	```
	this will add the image to the firebase storage and update the database.
* Let's bind the method and call it on button press
	* `this.addData = this.addData.bind(this);`
	* `onPress={this.addData}`  

If everything went well and you can see your data in firebase, you've just made your app much more cooler. Let's submit on to [Part IX](http://localhost:4000/react-native-bootcamp/part9/authentication.html)...(don't worry almost there) 