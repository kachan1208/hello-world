# Virgil Security Objective-C/Swift PFS SDK

![VirgilSDK](https://cloud.githubusercontent.com/assets/6513916/19643783/bfbf78be-99f4-11e6-8d5a-a43394f2b9b2.png)

[![Build Status](https://api.travis-ci.org/VirgilSecurity/virgil-sdk-x.svg?branch=master)](https://travis-ci.org/VirgilSecurity/virgil-sdk-x)
[![CocoaPods Compatible](https://img.shields.io/cocoapods/v/VirgilSDK.svg)](https://img.shields.io/cocoapods/v/VirgilSDK.svg)
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)
[![Platform](https://img.shields.io/cocoapods/p/VirgilSDK.svg?style=flat)](http://cocoadocs.org/docsets/VirgilSDK)
[![codecov.io](https://codecov.io/github/VirgilSecurity/virgil-sdk-x/coverage.svg)](https://codecov.io/github/VirgilSecurity/virgil-sdk-x/)
[![GitHub license](https://img.shields.io/badge/license-BSD%203--Clause-blue.svg)](https://github.com/VirgilSecurity/virgil/blob/master/LICENSE)

[Installation](#installation) | [Encryption Example](#encryption-example) | [Initialization](#initialization) | [Documentation](#documentation) | [Support](#support)

[Virgil Security](https://virgilsecurity.com) provides a set of APIs for adding security to any application. In a few simple steps you can encrypt communication, securely store data, provide passwordless login, and ensure data integrity.

For a full overview head over to our Objective-C/Swift [Get Started](https://developer.virgilsecurity.com/docs/swift/guides) guides.

## Installation

**Virgil PFS SDK** package includes the interdependent **Virgil SDK** package. The **Virgil SDK** is provided as module inside framework named **VirgilSDK**. VirgilSDK depends on another Virgil module called VirgilCrypto also packed inside framework named **VirgilCrypto**.
Both packages are distributed via Carthage and CocoaPods. Carthage is RECOMMENDED way to integrate VirgilSDK into your projects. Carthage integration is easy, convenient and you can simultaniously use CocoaPods to manage all other dependencies. CocoaPods support for versions above 4.5.0 is SUSPENDED, more info below under CocoaPods section.

Packages are available for iOS 7.0+ and macOS 10.10+.

To link frameworks to your project follow instructions depending on package manager of your choice:

### Carthage

[Carthage](https://github.com/Carthage/Carthage) is a decentralized dependency manager that builds your dependencies and provides you with binary frameworks.

You can install Carthage with [Homebrew](http://brew.sh/) using the following command:

```bash
$ brew update
$ brew install carthage
```

To integrate VirgilSDKPFS into your Xcode project using Carthage, perform following steps:

##### If you're building for iOS

1. Create an empty file with name `Cartfile` in your project's root folder, that lists the frameworks you’d like to use in your project.
1. Add the following line to your `Cartfile`

    ```ogdl
    github "VirgilSecurity/virgil-sdk-pfs-x" ~> "master"
    ```

1. Run `carthage update`. This will fetch dependencies into a `Carthage/Checkouts` folder inside your project's folder, then build each one or download a pre-compiled framework.
1. On your application targets’ “General” settings tab, in the “Linked Frameworks and Libraries” section, add each framework you want to use from the `Carthage/Build` folder inside your project's folder.
1. On your application targets’ “Build Phases” settings tab, click the “+” icon and choose “New Run Script Phase”. Create a Run Script in which you specify your shell (ex: `/bin/sh`), add the following contents to the script area below the shell:

  ```sh
  /usr/local/bin/carthage copy-frameworks
  ```

  and the paths to the frameworks you want to use under “Input Files”, e.g.:

  ```
  $(SRCROOT)/Carthage/Build/iOS/VirgilCrypto.framework
  $(SRCROOT)/Carthage/Build/iOS/VirgilSDK.framework
  $(SRCROOT)/Carthage/Build/iOS/VirgilSDKPFS.framework
  ```

This script works around an [App Store submission bug](http://www.openradar.me/radar?id=6409498411401216) triggered by universal binaries and ensures that necessary bitcode-related files and dSYMs are copied when archiving.

With the debug information copied into the built products directory, Xcode will be able to symbolicate the stack trace whenever you stop at a breakpoint. This will also enable you to step through third-party code in the debugger.

When archiving your application for submission to the App Store or TestFlight, Xcode will also copy these files into the dSYMs subdirectory of your application’s `.xcarchive` bundle.


To import VirgilSDK and VirgilCrypto after linking frameworks to your project add following lines to your source files:


###### Swift
``` swift
import VirgilCrypto
import VirgilSDK
```

## Swift note

Although VirgilSDK pod is using Objective-C as its primary language it might be quite easily used in a Swift application.
All public API is available from Swift and is bridged using NS_SWIFT_NAME where needed.

__Next:__ [Get Started with the Objective-C/Swift SDK](https://developer.virgilsecurity.com/docs/swift/get-started/encrypted-communication).

# Encryption Example

Virgil Security makes it super easy to add encryption to any application. With our Virgil PFS SDK you create a public [Virgil Card](https://developer.virgilsecurity.com/docs/swift/get-started/perfect-forward-secrecy#register-users) for every one of your users and devices. With these in place you can easily encrypt any data in the client.

```swift
// find Alice's card(s)
virgil.cards.searchCards(withIdentities: ["alice"]) { aliceCards, error in
  // encrypt the message using Alice's cards
  let message = "Hello Alice!"
  let encryptedMessage = try! virgil.encrypt(message, for: aliceCards!)

  // transmit the message with your preferred technology
  self.transmit(message: encryptedMessage.base64EncodedString())
}
```

The receiving user then uses their stored __private key__ to decrypt the message.


```swift
// load Alice's Key from storage.
let aliceKey = try! virgil.keys.loadKey(withName: "alice_key_1", password: "mypassword")

// decrypt the message using the key
let originalMessage = String(data: try! aliceKey.decrypt(transferData), encoding: .utf8)!
```

__Next:__ To [get you properly started][_guide_encryption] you'll need to know how to create and store Virgil Cards. Our [Get Started guide][_guide_encryption] will get you there all the way.

__Also:__ [Encrypted communication][_getstarted_encryption] is just one of the few things our SDK can do. Have a look at our guides on  [Encrypted Storage][_getstarted_storage], [Data Integrity][_getstarted_data_integrity] and [Passwordless Login][_getstarted_passwordless_login] for more information.

## Initialization

To use this SDK you need to [sign up for an account](https://developer.virgilsecurity.com/account/signup) and create your first __application__. Make sure to save the __app id__, __private key__ and it's __password__. After this, create an __application token__ for your application to make authenticated requests from your clients.

To initialize the SDK on the client side you will only need the __access token__ you created.

```swift
let virgil = VSSVirgilApi(token: "[ACCESS_TOKEN]")
```

> __Note:__ this client will have limited capabilities. For example, it will be able to generate new __Cards__ but it will need a server-side client to transmit these to Virgil.

To initialize the SDK on the server side we will need the __access token__, __app id__ and the __App Key__ you created on the [Developer Dashboard](https://developer.virgilsecurity.com/account/dashboard).

```swift
let url = Bundle.main.url(forResource: "[YOUR_APP_KEY_FILENAME_HERE]", withExtension: nil)!
let appPrivateKeyData = try! Data(contentsOf: url)
let credentials = VSSCredentials(appKeyData: appPrivateKeyData, appKeyPassword: "[YOUR_APP_KEY_PASSWORD_HERE]", appId: "[YOUR_APP_ID_HERE]")

let context = VSSVirgilApiContext(crypto: nil, token: "[YOUR_ACCESS_TOKEN_HERE]", credentials: credentials, cardVerifiers: nil)

let virgil = VSSVirgilApi(context: context)
```


## Documentation

Virgil Security has a powerful set of APIs, and the documentation is there to get you started today.

* Get Started documentation:
  * [Perfect Forward Secrecy](https://developer.virgilsecurity.com/docs/swift/get-started/perfect-forward-secrecy)
* [Reference API](https://developer.virgilsecurity.com/docs/references)


## License

This library is released under the [3-clause BSD License](LICENSE.md).

## Support

Our developer support team is here to help you. You can find us on [Twitter](https://twitter.com/virgilsecurity) or send us email support@virgilsecurity.com

[__support_email]: https://google.com.ua/
[_getstarted_root]: https://developer.virgilsecurity.com/docs/swift/get-started
[_getstarted]: https://developer.virgilsecurity.com/docs/swift/guides
[_getstarted_encryption]: https://developer.virgilsecurity.com/docs/swift/get-started/encrypted-communication
[_getstarted_storage]: https://developer.virgilsecurity.com/docs/swift/get-started/encrypted-storage
[_getstarted_data_integrity]: https://developer.virgilsecurity.com/docs/swift/get-started/data-integrity
[_getstarted_passwordless_login]: https://developer.virgilsecurity.com/docs/swift/get-started/passwordless-authentication
[_guides]: https://developer.virgilsecurity.com/docs/swift/guides
[_guide_initialization]: https://developer.virgilsecurity.com/docs/swift/guides/settings/install-sdk
[_guide_virgil_cards]: https://developer.virgilsecurity.com/docs/swift/guides/virgil-card/creating
[_guide_virgil_keys]: https://developer.virgilsecurity.com/docs/swift/guides/virgil-key/generating
[_guide_encryption]: https://developer.virgilsecurity.com/docs/swift/guides/encryption/encrypting
[_initialize_root]: https://developer.virgilsecurity.com/docs/swift/guides/settings/initialize-sdk-on-client
[_reference_api]: http://virgilsecurity.github.io/virgil-sdk-x/
