# react-native-uploadcare
Quick tutorial on uploading images to the image hosting+ service, Uploadcare from a React Native app

I spent way too long looking for a fancy library. Uploadcare has stuff for Javascript that apparently works well with React, and they do have a library for iOS. But React Native doesn't really need the whole widget. 

React Native has APIs to access photos, CameraRoll and ImagePickerIOS, and there are a few helpful libraries that have a bit more functionality. On iOS, they return something like this: `assets-library://asset/asset.JPG?id=106E99A1-4F6A-45A2-B320-B0AD4A8E8473&ext=JPG` 

The [Uploadcare Docs](https://uploadcare.com/docs/api_reference/upload/request_based/) show how to structure the request. The configurations also go in the body of the request. You might want to put your public key in a .env file, but for this example, I'm using their demo key. 

```javascript
export const photoUploader = async photoUri => {
  const photo = {
    uri: photoUri,
    type: 'image/jpg'
    };
  const body = new FormData();
  body.append('file', photo);
  body.append('UPLOADCARE_PUB_KEY', 'demopublickey');
  body.append('UPLOADCARE_STORE', '1');
  
  try {
    let response = await fetch('https://upload.uploadcare.com/base/', {
      method: 'POST',
      body
    });
    return JSON.parse(response._bodyText);
  catch(e){
    console.log(e)
  }
}
```  
This function should return something like this `{"file": your photo's uuid}`

Note: I'm using a bunch of newer Javascript stuff: aync/await, fetch, object shorthand, but this should work fine with XHR and .thens. 

Hopefully this saved someone else from my pain and wasted hours looking at unhelpful libraries.  
