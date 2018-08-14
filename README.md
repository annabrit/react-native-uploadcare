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


##Part 2 Things broke + Axios

Somewhere between time passing and moving to use Axios rather than fetch, the uploader function broke. 

Notes:
1. It looks like UploadCare expects the file to be an object with keys of uri, type, and name. 
2. FormData in React Native does not have all methods that FormData web api provides. We have to use append to add key/value pairs, not set. 
3. If you put your public key in your .env file, remember to rebuild your project. :P

The photo parameter is the entire file object returned by the image picker. I used the image picker from react-native-image-picker. 

```javascript

export async function uploadPhoto(photo) {
  const url = 'https://upload.uploadcare.com/base/';
  let body = new FormData();
  body.append('file', {
    name: photo.fileName,
    type: 'image/jpg',
    uri: photo.uri
  });
  body.append('UPLOADCARE_PUB_KEY', 'demopublickey');
  body.append('UPLOADCARE_STORE', '1');

  try {
    let response = await axios.post(url, body, { timeout: 20000 });
    return response.data;
  } catch (e) {
    console.log(e.response.data);
    return Promise.reject(e);
  }
}
```

A successful request return should look like `{file: <uuid>}`

Final notes:

If you're testing the endpoint with Insomnia, choose Multipart Form so you can select file for value type for your file field.
You can also paste the example cURL request from the UploadCare docs into the field just right of the drop down menu for request type, and it will format it for you. 

If you're testing the endpoint with Postman, choose Form Data and select file for value type for your file field. 
