[![Made by](https://img.shields.io/badge/Made_by-opsway-blue.svg)](https://opsway.com)
[![](https://img.shields.io/npm/v/@faisalali19/react-native-paytm.svg)](https://www.npmjs.com/package/@faisalali19/react-native-paytm)
[![](https://img.shields.io/npm/dm/@faisalali19/react-native-paytm.svg)](https://www.npmjs.com/package/@faisalali19/react-native-paytm)

# @faisalali19/react-native-paytm

This library has been forked from https://github.com/elanic-tech/react-native-paytm
Updated it to work with the latest version of react-native and latest PayTM SDK.

### Installation

| react-native-paytm | react-native |
| :----------------- | :----------- |
| 1.0.11             | < 0.60       |
| 1.0.12             | >= 0.60      |

```bash
npm i --save @faisalali19/react-native-paytm
```

or

```bash
yarn add @faisalali19/react-native-paytm
```

#### For RN < 0.60

Link it:

```bash
react-native link @faisalali19/react-native-paytm
```

#### For RN >= 0.60

Use CocoaPods installation if auto-linking doesn't work.

### CocoaPods

Add to your Podfile:

```
pod 'RNPayTm', :path => '../node_modules/@faisalali19/react-native-paytm'
```

Install it:

```bash
pod install && pod update
```

If it's not gonna work, then add `libPaymentsSDK.a` to "Link Binary With Libraries" Build Phase for the RNPayTm target (in Pods project).

### iOS (Manually)

1. In XCode, in the project navigator, right click `Libraries` ➜ `Add Files to [your project's name]`
2. Go to `node_modules/@faisalali19/react-native-paytm` and add `RNPayTm.xcodeproj`
3. In XCode, in the project navigator, select your project. Add `libRNPayTm.a` to your project's `Build Phases` ➜ `Link Binary With Libraries`
4. Run your project (`Cmd+R`)

## Methods

_`startPayment(details: PayTmPaymentDetails): void`_

```flow js
type PayTmPaymentDetails = {
  mode: 'Staging' | 'Production',
  MID: string,
  INDUSTRY_TYPE_ID: string,
  WEBSITE: string,
  CHANNEL_ID: string,
  TXN_AMOUNT: string,
  ORDER_ID: string,
  EMAIL?: string,
  MOBILE_NO?: string,
  CUST_ID: string,
  CHECKSUMHASH: string,
  CALLBACK_URL: string,
  MERC_UNQ_REF?: string,
};
```

## Usage

For more details check official documentation: [iOS](https://developer.paytm.com/docs/v1/ios-sdk/) and [Android](https://developer.paytm.com/docs/v1/android-sdk).

Example:

```javascript
import React from 'react';
import { Platform } from 'react-native';
import Paytm from '@faisalali19/react-native-paytm';

// Data received from PayTM
const paytmConfig = {
  MID: 'Value from PayTM dashboard',
  WEBSITE: 'Value from PayTM dashboard',
  CHANNEL_ID: 'WAP',
  INDUSTRY_TYPE_ID: 'Retail',
  CALLBACK_URL: 'https://securegw.paytm.in/theia/paytmCallback?ORDER_ID=',
};

export default class Test extends React.Component {
  componentWillMount() {
    Paytm.addListener(Paytm.Events.PAYTM_RESPONSE, this.onPayTmResponse);
  }

  componentWillUnmount() {
    Paytm.removeListener(Paytm.Events.PAYTM_RESPONSE, this.onPayTmResponse);
  }

  onPayTmResponse = (resp) => {
    const { STATUS, status, response } = resp;

    if (Platform.OS === 'ios') {
      if (status === 'Success') {
        const jsonResponse = JSON.parse(response);
        const { STATUS } = jsonResponse;

        if (STATUS && STATUS === 'TXN_SUCCESS') {
          // Payment succeed!
        }
      }
    } else {
      if (STATUS && STATUS === 'TXN_SUCCESS') {
        // Payment succeed!
      }
    }
  };

  runTransaction(amount, customerId, orderId, mobile, email, checkSum, mercUnqRef) {
    const callbackUrl = `${paytmConfig.CALLBACK_URL}${orderId}`;
    const details = {
      mode: 'Staging', // 'Staging' or 'Production'
      MID: paytmConfig.MID,
      INDUSTRY_TYPE_ID: paytmConfig.INDUSTRY_TYPE_ID,
      WEBSITE: paytmConfig.WEBSITE,
      CHANNEL_ID: paytmConfig.CHANNEL_ID,
      TXN_AMOUNT: `${amount}`, // String
      ORDER_ID: orderId, // String
      EMAIL: email, // String
      MOBILE_NO: mobile, // String
      CUST_ID: customerId, // String
      CHECKSUMHASH: checkSum, //From your server using PayTM Checksum Utility
      CALLBACK_URL: callbackUrl,
      MERC_UNQ_REF: mercUnqRef, // optional
    };

    Paytm.startPayment(details);
  }
}
```
