---
layout: page
title:  Detail Screen
category: part4
---

Our Main Screen shows a List of Moments, Let's add feature when the user click on a Moment He's taken to a Detail Screen where it shows the Image, Title and A Map with the Location of the Moment. Our Completed Detail Screen would look like the following Screenshot.
![Detail Screen]({{ site.url }}{{site.baseurl}}/assets/detail-screen.png) 
### Detail Screen
Let's now implement the Detail Screen,
* create a new file with name `DetailScreen.js`
* add a basic react component code which returns text, like this..
	```
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Button
} from 'react-native';
import { Icon, ListItem, Tile } from 'react-native-elements';
export default class DetailScreen extends Component {
  render() {
    return (
      <Text>Hello Detail Screen</Text>
    );
  }
}
	```  
* Now Let's add the Detail Screen to the Stacknavigator
	- `import DetailScreen from './components/DetailScreen.js';`  
	- add DetailScreen to StackNavigator
		```
const TravelLogStackNav = StackNavigator({
	Main: {screen: MainScreen},
	Detail : {screen : DetailScreen},
});
		```	
		if you wanna see if your Details Screen is working properly, just swap the order of the screens in the Stacknavigator.  

### _Navigate and Props_
All is well and good but how do we actually navigate from Main Screen to Detail Screen. React navigation gives a cool Prop named navigate, which allows to navigate from one screen to another. Since you've already taken ReactJS bootcamp, you should have a pretty good understanding of [Props](https://facebook.github.io/react-native/docs/props.html)(If you don't? "abort! abort!"). Props are the way different components talk to each other.
So in Main Screen we have a list of moments which when clicked should navigate to detail screen. Let's implement that,
[from react-native-elements documentation] we can pass a prop `onPress` to a ListItem which should be set to a function. So let's refactor our `renderListItems` return a listitems with `onPress` prop. after adding `onPress` prop our `renderListItems` should look like...
```
renderListItems(moment_obj,index) {
  if(index == 0)
	  return (
	    <Tile
	      imageSrc={{ "{{ uri: moment_obj.image" }}}}
	      title={moment_obj.title}
	      featured
	      key={index}
	      onPress={() =>{
            this.props.navigation.navigate('Detail');
          }
          }
	    />
	  );
  else
	  return (
	    <ListItem
	      roundAvatar
	      avatar={{ "{{ uri: moment_obj.image" }}}}
	      key={index}
	      title={moment_obj.title}
	      onPress={() =>{
            this.props.navigation.navigate('Detail');
          }
          }
	    />
	  );
}
```
you also must bind the method, since we are using `this.props` inside `renderListItems` method.
```
constructor(props){ 
    super(props);
    this.renderListItems = this.renderListItems.bind(this);
  }
```  
Now if you go back to emulator and reload(**menu->reload**), you'd see the Main Screen and when you click on one of the ListItems it would take you to the Details Screen. But it isn't doing anything fancy, is it? now let's send the moment data from Main Screen to Detail Screen and show it the detail Screen.  
React Navigation `navigate` action takes another argument that again can be retrieved from the destination screen. so you can changes the `onPress` prop to.
```
onPress={() =>{
            this.props.navigation.navigate('Detail', { details : moment_obj});
          }
```  
Now we can retrieve these moment details from the Detail Screen, lets' do that...  
From the `DetailScreen` render method declare a new variable `details` and set it to `this.props.navigation.state.params.details`. 
``` 
			var details = this.props.navigation.state.params.details;  
```
This will retrieve the details moments object we passed in the MainScreen. Let's see what this details variable is assigned to, by print it on the details screen. Go  to render method and inside return change `Hello Detail Screen` to `{JSON.stringify(details)}`. why use `JSON.stringify()`? because we are sending a JSON object from MainScreen and `<Text>` Component only accepts Text. If everything worked well, you should see some JSON data like this...
```
{"title" : "Playing at the beach","image" : "http://d33y93cfm0wb4z.cloudfront.net/Ella/client_content/banana_boat/beach_games.jpg","location" : {"lat" : 17.686816,"lon" : 83.218482}}
```  
  

Now let's use this data and use it to create our desired View. Let's discuss the pieces we need to put Detail Screen together. 1st there's the image at the top, followed by the title and a Map.
* Image and the Title can be shown as a single component as a Tile.
* We are gonna implement Map in the Later Parts, for now just put the Lat and Lon of the location as text.
* we'll be using Grids to split our Screen into Parts.

### _Grids_
We can split our screen into two part using Grid and Rows, one row would be used for Tile COmponent and the other would be used for Map Component. Let's see an example of a Grid...
```
import {Grid, Row} from 'react-native-elements';

<Grid>
  <Row></Row>
  <Row></Row>
</Grid>
```  
this will create a Grid Layout with two Rows. Lets use the same layout for `DetailScreen`. Change the render method to return the above Component(don't forget the import statement).

### __Components__
Now Let's add the Title Component to the first row.  
	```
	<Tile
		imageSrc={{ "{{ uri : details.image " }}}}
		title={details.title}
	/>
	```  
	This get's the image and title from our details object and passes as prop to the Tile Component.  
Add a Text Component to the Second Row.  
	`<Text>{details.location.lat},{details.location.lon}</Text>`  
	this will print the lat and lon of the coordinates in the second row, until we implement the Map View  
Now your completed render method should look  
```
  render() {
      var details = this.props.navigation.state.params.details;
    return (
    <Grid>
      <Row>
      <Tile
        imageSrc={{ "{{uri: details.image" }}}}
        title={details.title}
      />
      </Row>
      <Row>
    <Text>{details.location.lat},{details.location.lon}</Text>
      </Row>
    </Grid>
    );
  }
```  

And that's it for Part IV, we've successfully passed data from one Screen to Another and displayed it in our destination Screen using React navigation navigate. Let's catch fire with [Firebase](https://firebase.google.com/) in [Part V]({{ site.url }}{{site.baseurl}}/part5/firebase.html)...