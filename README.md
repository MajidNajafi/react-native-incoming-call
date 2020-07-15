# react-native-incoming-call

> React Native module to display custom incoming call activity, best result when using with firebase background messaging. Only for Android since iOS we have VoIP.

Yes I heard you could use **self managed ConnectionService** thing. But since I'm not an Android expert, this is a solution I found acceptable.

You could also wait for [this feature request](https://github.com/react-native-webrtc/react-native-callkeep/issues/43) from `react-native-callkeep` to be resolved and have an easier life.

## Getting started

`$ npm install react-native-incoming-call --save`

or

`$ yarn add react-native-incoming-call`

### Addition installation step

In `AndroidManifest.xml`, add `<activity android:name="com.incomingcall.UnlockScreenActivity" />` line between `<application>` tag.

For RN >= 0.60, it's done. Otherwise:

`$ react-native link react-native-incoming-call`

## Usage
```javascript
import {useEffect} from 'react';
import IncomingCall from 'react-native-incoming-call';

// Display incoming call activity. Should be called in backgroundHandler function of react-native-firebase.
IncomingCall.display(uuid, name, callType, avatar);

// Dismiss current activity. Should be called when call ended.
IncomingCall.dismiss();

// Listen to cancel and answer call events
useEffect(() => {
  if (Platform.OS === "android") {
    /**
     * App in background or killed state, if user press answer button
     * IncomingCall open app and put payload to getLaunchParameters
     * You could start the call action here.
     * End call action in this case not supported yet.
    */
    const payload = await IncomingCall.getLaunchParameters();
    console.log('launchParameters', payload);
    IncomingCall.clearLaunchParameters();
    if (payload) {
      // Start call here
    }

    /**
     * App in foreground: listen to call events and determine what to do next
    */
    DeviceEventEmitter.addListener("endCall", payload => {
      // End call action here
    });
    DeviceEventEmitter.addListener("answerCall", payload => {
      // Start call action here
    });
  }
}, []);
```
