## React Native Call Detection 🎉 🎊
[![npm version](https://badge.fury.io/js/react-native-call-detection.svg)](https://badge.fury.io/js/react-native-call-detection)

This package helps to detect different call states like `Incoming`, `Disconnected`, `Dialing` and `Connected` for iOS. For android, this package will give following states, `Offhook`, `Incoming`, `Disconnected` and `Missed`. In the case of `Incoming` for android, the package will also provide with the incoming phone number.

## Installation

Add the package to your react-native project in the following way

```shell
yarn add react-native-call-detection

```

### For Android:-
Autolinking should work without manual changes

## Usage
There are different hooks that you may get depending on the platform. Since for android you could also request the package to provide you with phone number of the caller, you will have to provide the necessary request message and the corresponding error callback. The package will request for `READ_PHONE_STATE` permission in android.

Its really easy to setup the package. Have a look at the following code snippet

``` javascript
import React from 'react';
import {
  StyleSheet,
  Text,
  View,
  TouchableHighlight,
  PermissionsAndroid,
} from 'react-native';
import CallDetectorManager from 'react-native-call-detection'; export default class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      featureOn: false,
      incoming: false,
      number: null,
      event: null
    };
  } componentDidMount() {
    this.askPermission();
  } askPermission = async () => {
    try {
      const permissions = await PermissionsAndroid.requestMultiple(
        [
          PermissionsAndroid.PERMISSIONS.READ_CALL_LOG,
          PermissionsAndroid.PERMISSIONS.READ_PHONE_STATE,
        ]);
      console.log('Permissions are:', permissions);
    } catch (err) {
      console.warn(err);
    }
  }; startListenerTapped = () => {
    this.setState({ featureOn: true });
    this.callDetector = new CallDetectorManager(
      (event, number) => {
        if (event === 'Disconnected') {
          // Do something call got disconnected
          this.setState({ incoming: false, number: null, event });
        } else if (event === 'Incoming') {
          // Do something call got incoming
          this.setState({ incoming: true, number, event });
        } else if (event === 'Offhook') {
          //Device call state: Off-hook.
          // At least one call exists that is dialing,
          // active, or on hold,
          // and no calls are ringing or waiting.
          this.setState({ incoming: true, number, event });
        } else if (event === 'Missed') {
          // Do something call got missed
          this.setState({ incoming: false, number: null, event });
        }
      },
      true, // if you want to read the phone number of the incoming call [ANDROID], otherwise false
      () => { }, // callback if your permission got denied [ANDROID] [only if you want to read incoming number] default: console.error
      {
        title: 'Phone State Permission',
        message:
          'This app needs access to your phone state in order to react and/or to adapt to incoming calls.',
      }, // a custom permission request message to explain to your user, why you need the permission [recommended] - this is the default one
    );
  }; stopListenerTapped = () => {
    this.setState({ featureOn: false });
    this.callDetector && this.callDetector.dispose();
  }; render() {
    return (
      <View style={styles.body}>
        <Text style={styles.text}>{this.state.event}</Text>
        <TouchableHighlight
          onPress={
            this.state.featureOn
              ? this.stopListenerTapped
              : this.startListenerTapped
          }>
          <View
            style={{
              width: 100,
              height: 50,
              justifyContent: 'center',
              alignItems: 'center',
              backgroundColor: this.state.featureOn
                ? 'greenyellow'
                : 'indianred',
            }}>
            <Text style={styles.text}>
              {this.state.featureOn ? `ON` : `OFF`}{' '}
            </Text>
          </View>
        </TouchableHighlight>
        {this.state.incoming && (
          <Text style={{ fontSize: 50 }}>number {this.state.number}</Text>
        )}
      </View>
    );
  }
} const styles = StyleSheet.create({
  body: {
    backgroundColor: 'honeydew',
    justifyContent: 'center',
    alignItems: 'center',
    flex: 1,
  },
  text: {
    padding: 20,
    fontSize: 20,
  },
  button: {},
});
```

### How to run an example

1. Install `node` and `watchman`

	```
	brew install node
	brew install watchman

	```

2. `yarn`

    Install `yarn` from `npm`.

        npm i -g yarn

3. Xcode

    Install it from the App Store.

4. React Native Debugger

    This is an electron app that bundles react devtools, redux devtools and chrome devtools configured for use with react-native.

        brew cask install react-native-debugger
5. Once you have done all the above steps, navigate to `CallDetectionExample` folder and run `yarn` or `npm install`, it will fetch all the dependencies in the `node_modules` folder.

6. Run the packager
	`npm start`

7. Update the mobile number of your friend in [`HomeComponent.js`](CallDetectionExample/src/HomeComponent.js)


	```javascript
	 callFriendTapped() {
	 // Add the telephone number
    	Linking.openURL('tel:5555555555')
      .catch(err => {
        console.log(err)
      });
  	 }

	```

7. To run the example on iOS from terminal type
	`react-native run-ios` (This will open the simulator, since simulator doesnt have the support for calling I will advice you to connect your iOS device and the follow the below procedure for running the app through xcode)

	or you can also run the app from xcode, for that, open `/CallDectionExample/ios/CallDetectionExample.xcodeproj` in xcode and run on the device.

8. To run the example on android, connect any android device to your mac then follow the below steps
	1. Navigate to `android` folder(./CallDectionExample/android/) folder and then run `adb reverse tcp:8081 tcp:8081`
	2. Navigate back to example directory(CallDectionExample) and then run
	`react-native run-android`

For any problems and doubt raise an issue.
