---
layout: page
title:  Form Screen
category: part8
---

So we have implemented a Main Screen but we aren't displaying any data of our own to it, are we? So let's add a Form Screen which let's us upload an Image, and add some data related to the moment into the Database. So Let's Get Started...

### _Firebase Upload_
In our Last Bootcamp we already learned how to upload images to [Firebase Storage](https://firebase.google.com/docs/storage/){:target="_blank"}. In React Native, Firebase file upload won't be that straight forward. To Upload to firebase we must first create blob from file URI, then we can upload the blob to firebase with `firebase.storage().ref().put()`. So How do we create a Blob from a image URI? There is React Native module with the name [react-native-fetch-blob](https://github.com/wkh237/react-native-fetch-blob){:target="_blank"}. this module given a resource URI converts it into a blob. Let's install 
[react-native-fetch-blob](https://github.com/wkh237/react-native-fetch-blob){:target="_blank"}. run these two commands to install and link react-native-fetch-blob  
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
	As I said before we need [React Native Fetch Blob](https://github.com/wkh237/react-native-fetch-blob){:target="_blank"} to be able to upload images to firebase. to be able to use it have to do an import and a bunch of initializations. add the following lines after the import statements...  
	```
import RNFetchBlob from 'react-native-fetch-blob';
const Blob = RNFetchBlob.polyfill.Blob;
const fs = RNFetchBlob.fs;
window.XMLHttpRequest = RNFetchBlob.polyfill.XMLHttpRequest;
window.Blob = Blob;
	```
	With this done let's create a method `uploadImage` that takes three arguments a name, a file uri(in our case an image) and an optional mime argument. here's the method..
	```
  uploadImage(moment_time,uri, mime = 'image/jpg') {
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
	So what this method does is given a file URI and a name it read the file and return a Blob created from it, then the blob is out to the firebase with firebase.storage().ref().put() function. then it closes the blob and returns the downloadURl from firebase. THe above method also uses `Platform` module so you have to import it from `react-native`
* Also we need to get the Geo Location for the maps, in react-native getting the location is straight forward. all we have to do is use `navigator.geolocation` same as we do in the web., let's get the current location of the user in the `componentDidMount()` of `FormScreen.js`. So now should have a `componentDidMount()` that looks something like this..
```
componentDidMount(){
      navigator.geolocation.getCurrentPosition(
         (position) => {
            this.setState({ lat : position.coords.latitude, lon : position.coords.longitude });
         },
         (error) => alert(error.message),
         { enableHighAccuracy: false, timeout: 20000, maximumAge: 1000 }
      );
  }
```
* Now let's add a method that gets called when our Button is pressed  
	```
	addData(){
		this.uploadImage(this.props.navigation.state.params.moment_details.moment_time,this.props.navigation.state.params.moment_details.image_path);
		const moment_details = this.props.navigation.state.params.moment_details;
		this.databaseRef = firebase.database().ref();
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

### _Main Screen_  
if you noticed, we are setting a file path and not a complete URL to our `image` key in our database. So if we try to run our app as it is, we won't see the image of the moment. Let's change that...
* Your current `componentDidMount` of `MainScreen` should look like  
```
  componentDidMount() {
      try{
        this.momentsRef.on('value', (snap) => {
          this.setState({
            moments : Object.keys(snap.val()).map((orderItemKey,index) => {return snap.val()[orderItemKey]})
          });
          console.log("apples_after 2"+JSON.stringify(this.state.items) + "\n");
        });
      }catch(e){
        console.log(e);
      }
  }
```  
refactor it and move the code to a new method named `listenForItems` and call it from `componentDidMount`. now your code should look like...
```
  componentDidMount() {
    this.listenForItems(this.momentsRef);
  }
  listenForItems(momentsRef) {
    try{
      momentsRef.on('value', (snap) => {
        if (snap.val() != null) {
          this.setState({moments : []});
          var temp_moments = Object.keys(snap.val()).map((orderItemKey,index) => {
              let storage = firebase.storage();
              let imageRef = storage.ref().child(""+snap.val()[orderItemKey].image);
              let imageURL = imageRef.getDownloadURL().then((imgURL) => {
                  var temp_obj = snap.val()[orderItemKey];
                  temp_obj["image"] = imgURL;
                  var temp_mom = this.state.moments;
                  temp_mom[index] = temp_obj;
                  this.setState({ moments: temp_mom });
                }).catch((error)=>{console.log(error)});
              return snap.val()[orderItemKey]
            });
          this.setState({moments : temp_moments});
        }
      });
    }catch(e){
      console.log(e);
    }
  }
```  
So what's this code doing? In `ListenForItems` we try to get the value from `momentsRef` with `momentsRef.on('value'`. when we get the snapshot from firebase we see if it's not null with `snap.val() != null`. If it's not null we empty out moments array with `this.setState({moments : []});`. Then we assign to `temp_moments` an array of moments returned by `Object.keys(snap.val()).map`.  
Hey dude, that all fine but what these vague `imageRef`,`imageURL` etc. doing in code?  
As I said we are adding the path to the image to our database and not the actual image, so we have to get image URL with `getDownloadURL()` from firebase.  
```
              let storage = firebase.storage();
              let imageRef = storage.ref().child(""+snap.val()[orderItemKey].image);
              let imageURL = imageRef.getDownloadURL().then((imgURL) => {
                  var temp_obj = snap.val()[orderItemKey];
                  temp_obj["image"] = imgURL;
                  var temp_mom = this.state.moments;
                  temp_mom[index] = temp_obj;
                  this.setState({ moments: temp_mom });
                }).catch((error)=>{console.log(error)});
```  
So what these three lines of code does is, defines a storage variable and assigns it to `firebase.storage()`. and then get the image reference and assign it to `storage.ref().child(""+snap.val()[orderItemKey].image)`, which would give us the image reference of current moment inside `map` function. we then get the image URL from the reference with `imageRef.getDownloadURL()`, and get the imageURL in the callback function of `.then`. We now have a URL for our image, and all we have to do is update our current object in moments state. We assign the current object to a temporary variable called `temp_obj` and update it's image element to the image URL from firebase. Now we get the moments state and assign it to `temp_mom` and update the value at index `index` to `temp_obj`. Now we set the moments state back `this.setState({ moments: temp_mom });` with updated image value.


If everything went well and you can see your data in firebase and on Main Screen, you've just made your app much more cooler. Let's submit on to [Part IX]({{ site.url }}{{site.baseurl}}/part9/authentication.html)...(don't worry almost there) 