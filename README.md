# Virgil Security Objective-C/Swift PFS SDK

![VirgilSDK](https://cloud.githubusercontent.com/assets/6513916/19643783/bfbf78be-99f4-11e6-8d5a-a43394f2b9b2.png)

[![Build Status](https://api.travis-ci.org/VirgilSecurity/virgil-sdk-x.svg?branch=master)](https://travis-ci.org/VirgilSecurity/virgil-sdk-x)
[![CocoaPods Compatible](https://img.shields.io/cocoapods/v/VirgilSDK.svg)](https://img.shields.io/cocoapods/v/VirgilSDK.svg)
[![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage)
[![Platform](https://img.shields.io/cocoapods/p/VirgilSDK.svg?style=flat)](http://cocoadocs.org/docsets/VirgilSDK)
[![codecov.io](https://codecov.io/github/VirgilSecurity/virgil-sdk-x/coverage.svg)](https://codecov.io/github/VirgilSecurity/virgil-sdk-x/)
[![GitHub license](https://img.shields.io/badge/license-BSD%203--Clause-blue.svg)](https://github.com/VirgilSecurity/virgil/blob/master/LICENSE)

[Installation](#installation) | [Encryption Example](#encryption-example) | [Initialization](#initialization) | [Documentation](#documentation) | [Support](#support)

[Virgil Security](https://virgilsecurity.com) provides a set of APIs for adding security to any application. In a few simple steps you can encrypt communication, securely store data, provide passwordless login, ensure data integrity, and add perfect forward secrecy in communications.

Perfect Forward Secrecy (PFS) is a technique, that protects previously intercepted traffic from being decrypted even if the main private key is compromised. For a full overview head over to our [website](https://developer.virgilsecurity.com/docs/references/perfect-forward-secrecy).

## Installation

**Virgil PFS SDK** package includes the interdependent **Virgil SDK** package. The **Virgil SDK** is provided as module inside framework named **VirgilSDK**. VirgilSDK depends on another Virgil module called VirgilCrypto also packed inside framework named **VirgilCrypto**.
All packages are distributed via Carthage and CocoaPods. Carthage is RECOMMENDED way to integrate Virgil PFS SDK into your projects. Carthage integration is easy, convenient. CocoaPods support for versions above 4.5.0 is SUSPENDED, more info below under CocoaPods section.

Packages are available for iOS 7.0+.

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


To import Virgil PFS SDK and VirgilCrypto after linking frameworks to your project add following lines to your source files:


###### Swift
``` swift
import VirgilCrypto
import VirgilSDK
```

__Next:__ [Get Started with the Objective-C/Swift PFS SDK](https://developer.virgilsecurity.com/docs/swift/get-started/perfect-forward-secrecy).

# PFS Encryption Example

Virgil Security makes it super easy to add PFS to any communications. With our Virgil SDK you create a public [Virgil Card](https://developer.virgilsecurity.com/docs/swift/get-started/perfect-forward-secrecy#register-users) for every one of your users and devices. With these in place you can easily initialize PFS chat.

```swift
let secureChatPreferences = SecureChatPreferences (
    crypto: "[CRYPTO]",
    // identity Bob's Card
    identityCard: "[BOB_IDENTITY_CARD]",
    privateKey: "[BOB_PRIVATE_KEY]",
    accessToken: "[YOUR_ACCESS_TOKEN_HERE]")

self.secureChat = SecureChat(preferences: secureChatPreferences)

self.secureChat.rotateKeys(desiredNumberOfCards: 100) { error in
    //...
}
```

Once Recipients initialized a PFS Chat, they can communicate. User then uses session chat id to encrypt the message.


```swift
func sendMessage(forReceiver receiver: User,
    usingSession session: SecureSession, message: String) {
    let ciphertext: String
    do {
        // encrypt the message using previously initialized session
        ciphertext = try session.encrypt(message)
    }
    catch {
        // Error handling
        return
    }

    // send a cipher message to recipient using your messaging service
    self.messenger.sendMessage(
        forReceiverWithName: receiver.name, text: ciphertext)

```

__Next:__ To [get you properly started][_guide_encryption] you'll need to know how to create and store Virgil Cards. Our [Get Started guide][_guide_encryption] will get you there all the way.


## Initialization

To use this SDK you need to [sign up for an account](https://developer.virgilsecurity.com/account/signup) and create your first __application__. Make sure to save the __app id__, __private key__ and it's __password__. After this, create an __application token__ for your application to make authenticated requests from your clients.

To initialize the SDK on the client side you will only need the __access token__ you created.

```swift
let virgil = VSSVirgilApi(token: "[YOUR_ACCESS_TOKEN_HERE]")
```

> __Note:__ this client will have limited capabilities. For example, it will be able to generate new __Cards__ but it will need a server-side client to transmit these to Virgil.

To initialize the SDK on the server side we will need the __access token__, __app id__ and the __App Key__ you created on the [Developer Dashboard](https://developer.virgilsecurity.com/account/dashboard).


## Documentation

Virgil Security has a powerful set of APIs, and the documentation is there to get you started today.

* Get Started documentation:
  * [Perfect Forward Secrecy](https://developer.virgilsecurity.com/docs/swift/get-started/perfect-forward-secrecy)
* [Reference API](https://developer.virgilsecurity.com/docs/references)


## License

This library is released under the [3-clause BSD License](LICENSE.md).

## Support

Our developer support team is here to help you. You can find us on [Twitter](https://twitter.com/virgilsecurity) or send us email support@virgilsecurity.com
