---
layout: page
title:  React Navigation
category: part3
---

### _Intro_
We just created a Single Main Screen with ListItems from our dummy_data, but most of the time when you are creating apps you want to have more than one Screen. [React Navigation](https://github.com/react-community/react-navigation){:target="_blank"} Let's us do that, just think of it as [React Router](https://github.com/ReactTraining/react-router){:target="_blank"} for React Native. Just run the following command to install React Navigation,
<center><strong><code>npm install --save react-navigation</code></strong></center>



### _Types_
React Navigation gives three Navigator Components which you can again mix and match.
* [StackNavigator](https://reactnavigation.org/docs/navigators/stack#StackNavigator){:target="_blank"}  
	Let's us change Screens by stacking one top of another
* [TabNavigator](https://reactnavigation.org/docs/navigators/tab#TabNavigator){:target="_blank"}  
	Let's us change Screens with a tabs at the bottom
* [DrawerNavigator](https://reactnavigation.org/docs/navigators/drawer#DrawerNavigator){:target="_blank"}  
	Let's us set up a screen with a drawer navigation

### _Stacknavigator_
For this Project we'll be sticking to Stacknavigator. here's an example of Stacknavigator  
```
import React from 'react';
import {
  AppRegistry,
  Text,
} from 'react-native';
import { StackNavigator } from 'react-navigation';

class HomeScreen extends React.Component {
  static navigationOptions = {
    title: 'Welcome',
  };
  render() {
    return <Text>Hello, Navigation!</Text>;
  }
}

const SimpleApp = StackNavigator({
  Home: { screen: HomeScreen },
});

AppRegistry.registerComponent('SimpleApp', () => SimpleApp);
```  
__note :__ don't copy this code it's just an example
{: style="color:gray; font-size: 80%; text-align: center;"}


All it does is create a StackNavigator with a single screen called Home and assign it to the HomeScreen Component. Now Let's Change our code to make use of awesome React Navigation, install React Navigation if you haven't already and move on..


### _Refractor_  
* We'll first create a directory with name `components` to contain all our Screens
* Add a File with name `MainScreen.js` in the components directory
* Move All the Main Screen specific code from `index.android.js` to our `MainScreen.js` file, Now your MainScreen should look like
	```
import React, { Component } from 'react';
import {
  AppRegistry,
  StyleSheet,
  Text,
  View,
  Button,
  ScrollView
} from 'react-native';
import { Icon, ListItem, Tile } from 'react-native-elements';
export default class MainScreen extends Component {
  renderListItems(moment_obj,index) {
    if(index == 0)
      return (
          <Tile
            imageSrc={{ "{{ uri: moment_obj.image" }}}}
            title={moment_obj.title}
            featured
            key={index}
          />
      );
    else
      return (
        <ListItem
          roundAvatar
          avatar={{ "{{ uri: moment_obj.image" }}}}
          key={index}
          title={moment_obj.title}
        />
      );
  }
  render() {
    return (
      <View style={styles.container}>
          <ScrollView>
          {
            this.state.items.map(this.renderRow)
          }
          </ScrollView>
        <View style={styles.float}>
          <Icon raised name='add' color='#f50' onPress={() =>
            navigate('Camera', { name: 'Jane' })
          } />
        </View>
      </View>
    );
  }
}
const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
  float: {
    position: 'absolute',
    bottom: 10,
    right: 10,
  },
});
  const dummy_data = [your dummy data];
	```
* Now Our TraveLog Class in `index.android.js` should contain a empty render method. Let's make this render method return a Stacknavigator component.
	- first of all import Stacknavigator from `react-navigation`  
		`import { StackNavigator } from 'react-navigation';`
	- define a constant `TravelogStackNav` initialized to Stacknavigator
		```
const TravelLogStackNav = StackNavigator({
	Main: {screen: MainScreen},
},{
  navigationOptions : {
    header: null
  }
});
		```
		here we are initializing a Stacknavigator with the screen Main screen set to `MainScreen` component, the second argument is an Object that tell that we don't want a Title Bar for all of our Screens. but wait, we didn't import the MainScreen Component after moving to the new file. So let's import MainScreen Component with  
			`import MainScreen from './components/MainScreen.js';` 
	- make TravelLog Class return `TravelLogStackNav` component  
	```
render() {
	return (
		<TravelLogStackNav />
	);
}
	```  

Now If you reload the App in emulator, you'd see that nothing has changed but we now have React Navigation Added to our project and can now have a multi-screen app where we can navigate from one screen to other.  

With that We've completed PART III, let's navigate to [PART IV]({{ site.url }}{{site.baseurl}}/part4/detail-screen.html)...