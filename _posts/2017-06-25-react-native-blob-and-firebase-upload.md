---
layout: page
title:  Form Screen
category: part8
---

So we have implemented a Main Screen but we aren't displaying any data of our own to it, are we? So let's add a Form Screen which let's upload an Image, and add some data related to the moment into the Database. So Let's Get Started

### __Firebase Upload__
In our Last Bootcamp we already learned how to upload images to [Firebase Storage](https://firebase.google.com/docs/storage/). In React Native Firebase file upload won't be that straight forward. To Upload to firebase we must first create blob from file URI, then we can upload the blob to firebase with `firebase.storage().ref(),put()`. So How do we create a Blob from a image URI? There is React Native module with the name [react-native-fetch-blob](https://github.com/wkh237/react-native-fetch-blob). this module given a resource URI converts it into a blob. Let's install 
[react-native-fetch-blob](https://github.com/wkh237/react-native-fetch-blob). run these two commands to install and link react-native-fetch-blob  
* `npm install --save react-native-fetch-blob`
* `react-native link react-native-fetch-blob`  
With React Native Fetch Blob installed we can now start working on our Form Screen
### __Form Screen__