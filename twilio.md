# Add End-to-End Encryption to your Twilio Product using E3kit
In this tutorial, we will help you add end-to-end encryption to your product to secure your messages and user data that you deliver using Twilio Programmable Chat.



### How Does End-to-End Encryption Work With Twilio?
End-to-end encryption consists of securing data between two users or endpoints using a private & public key for each user or endpoint:

<a href="https://virgilsecurity.com"><img src="my_new_img/e3kit_twilio.jpg" width=100%></a>

- Twilio SDK on the Client side allows users to send and receive messages, join groups and communicate with Twilio backend in other ways, while Virgil handles encryption of users' messages and data.
- Virgil e3kit SDK on the Client side allows you to generate a public and a private key for your user.
- The public key is published to Virgil Cards Service, part of the Virgil Cloud PKI. When your users want to send a message, the Virgil SDK uses the recipient's public key to encrypt the message data in a way that only the recipient's corresponding private key can decrypt it.
- The private key is kept on the end-user's device, enabling the user and only the user to decrypt any messages or data that other users sent to them. It's similar to the relationship between a public mailing address and a private mailbox. You look up someone's address to send them a letter, but only they can unlock their mailbox to open and read the letter.

The address book (Virgil's Cards Service), mailing address (public key) and mailbox key (private key) are related to each other, but can't be traced to each other in any way that would compromise the security of the system. End-to-end encryption also locks the letter (message data), and only the recipient has the key to unlock it.

This setup enables users to encrypt a message on their phone or computer, send it over the Internet with the help of Twilio to a recipient without any chance of another party reading it in transit or on the server, and have it be decrypted only by the recipient on their phone or computer. This all works seamlessly for the end-users and it only takes a few lines of code to implement using e3kit SDK.


# Get Started

## Step 1: Set Up Your Backend
We assume that you already have a Twilio Project. If you don't, please create one now. Also, we assume that you have a basic backend server for your app. Your server must be able to create and store user records in persistent storage and provide a user authentication strategy, whether that's a simple username/password or a third-party provider like GitHub or Facebook, so make sure to implement that feature as well.

### Provide your users access to Virgil and Twilio Cloud
To make API calls to the Virgil Cloud, you'll need to provide the users of your app with a JWT that contains the user's `identity` which is a string that uniquely identifies each user in your application. For this tutorial we'll assume that your user records have a unique ID assigned by the database, known as `uid`, and we will use that as their `identity`. 

> You can use any string value for the user identity as long as it's unique for each user. For security reasons, avoid using fields that contain any personally identifiable information such as name or email address, especially if your product needs to comply with regulations such as HIPAA and GDPR.

JWTs must be generated on the server-side for several reasons:
- each JWT must be signed with your Virgil API Key to prove it was issued by you,
- the client side should not have access to your sensitive API Key and
- you need to authenticate the user making the request to get their identity.

> JWT grants access to Virgil Cloud for a specific Virgil Application. Each JWT grants a user access to the Virgil Cloud for a specific Virgil Application and has a limited lifetime that is configured by you. However, best practice is to generate a JWT for the shortest amount of time feasible for your application. If you need more details about the structure of a JWT, please take a look at this article.

The same situation with access to Twilio Cloud. In order to use a functionality of the Twilio Programmable Chat you have to provide your users with a Twilio JWT.

### Generate Virgil and Twilio JWT 

In order to be able to encrypt/decrypt messages and use a Twilio Programmable Chat, your users have to be authenticated using JWT at Virgil and Twilio services. So, you need a backend code that generates Virgil and Twilio JWTs with the help of Virgil SDK and Twilio Helper on your Server side. 

For this tutorial we've created a sample backend code that demonstrates how to combine Virgil and Twilio JWT generation. To setup and run the sample backend locally, head over to [Twilio-Sample-Backend-NodeJS](https://github.com/VirgilSecurity/twilio-sample-backend-nodejs) GitHub repo and follow the instructions in README. You can easily use it as an example to build own backend for a JWT generation.

## Step 2: Set Up Your Client
Virgil Security provides you with the Virgil `e3kit` SDK to create and store the user's private key on their device and publish the user's corresponding public key in the Virgil Cloud. 
Everything else except for the cryptographic functions is handled by Twilio SDK, which you'll have to [initialize](https://www.twilio.com/docs/chat/initializing-sdk-clients) on you Client side by yourself.

### Install e3kit
Use your package manager to download the Virgil e3kit SDK into your mobile or web project.

Javascript
```shell
npm install -S @virgilsecurity/e3kit
```

Swift
```shell
// CocoaPods is a dependency manager for Cocoa projects.
// You can install it with the following command:

$ gem install cocoapods

// to integrate Virgil E3Kit into your Xcode project using CocoaPods, specify it in your Podfile:

target '<Your Target Name>' do
use_frameworks!

pod 'VirgilE3Kit', '~> 0.3.0'
end

// then, run the following command:

$ pod install
```

### Initialize e3kit
In order to interact with the Virgil and Twilio Cloud, the Virgil e3kit SDK must be provided with a callback that it will call to fetch the Virgil and Twilio JWT from your backend for the current user.

```javascript
import { EThree } from '@virgilsecurity/e3kit';
// This function returns a token that will be used to authenticate requests
// to your backend.
// This is a simplified solution without any real protection, so here you need use your
// application authentication mechanism.
async function authenticate(identity) {
    const response = await fetch('http://localhost:3000/authenticate', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json',
        },
        body: JSON.stringify({
            identity: identity
        })
    });
    if (!response.ok) {
        throw new Error(`Error code: ${response.status} \nMessage: ${response.statusText}`);
    }
    return response.json().then(data => data.authToken);
}

// This function makes authenticated request to GET /virgil-jwt endpoint
// The token serves to make authenticated requests to Virgil Cloud
async function getVirgilToken(authToken) {
    const response = await fetch('http://localhost:3000/virgil-jwt', {
        headers: {
            // We use bearer authorization, but you can use any other mechanism.
            // The point is only, this endpoint should be protected.
            Authorization: `Bearer ${authToken}`,
        }
    })
    if (!response.ok) {
        throw new Error(`Error code: ${response.status} \nMessage: ${response.statusText}`);
    }

    // If request was successful we return Promise which will resolve with token string.
    return response.json().then(data => data.virgilToken);
}

authenticate('alice')
    .then(authToken => EThree.initialize(() => getVirgilToken(authToken)))
    .then(e3kit => /* e3kit initialized for 'alice' identity and ready for use */)
```

```swift
import VirgilE3Kit
import VirgilSDK

let connection = HttpConnection()

// This function returns a token that will be used to authenticate requests
// to your backend.
// This is a simplified solution without any real protection, so here you need
// use your application authentication mechanism.
let authCallback = { () -> String in
    let url = URL(string: "http://localhost:3000/authenticate")!
    let headers = ["Content-Type": "application/json"]
    let params = ["identity": identity]
    let requestBody = try! JSONSerialization.data(withJSONObject: params,
                                                  options: [])
    let request = Request(url: url, method: .post,
                          headers: headers, body: requestBody)
    let resonse = try! connection.send(request)

    let json = try! JSONSerialization.jsonObject(with: resonse.body!,
                                                 options: []) as! [String: Any]
    let authToken = json["authToken"] as! String

    return authToken
}

let authToken = authCallback()

let url = URL(string: "http://localhost:3000/virgil-jwt")!
// We use bearer authorization, but you can use any other mechanism.
// The point is only, this endpoint should be protected.
let headers = ["Content-Type": "application/json",
               "Authorization": "Bearer " + authToken]

// This function makes authenticated request to GET /virgil-jwt endpoint
// The token it returns serves to make authenticated requests to Virgil Cloud
let tokenCallback: EThree.RenewJwtCallback = { completion in
    let request = Request(url: url, method: .get, headers: headers)

    guard let response = try? connection.send(request),
        let body = response.body,
        let json = try? JSONSerialization.jsonObject(with: body, options: []) as? [String: Any],
        let jwtString = json?["virgilToken"] as? String else {
            completion(nil, AppError.gettingJwtFailed)
            return
    }

    completion(jwtString, nil)
}

// Initialize EThree SDK with get token callback to your sever
// E3kit uses the identity encoded in the JWT as the current user's identity
EThree.initialize(tokenCallback: tokenCallback) { eThree, error in
    // Init done!
}
```

```kotlin
//kotlin
```


The `EThree.initialize()` function gets the user's Virgil JWT, parses it, initializes the library and returns its instance, which is further used with user's `identity`. The `EThree.initialize()` function must be used on SignUp and SignIn flows.

### Initialize Twilio
```javascript
import Chat from 'twilio-chat';
// This function makes authenticated request to GET /twilio-jwt endpoint
// Returned token is used by twilio library
async function getTwilioToken(authToken) {
    const response = await fetch('http://localhost:3000/twilio-jwt', {
        headers: {
            Authorization: `Bearer ${authToken}`,
        }
    })
    if (!response.ok) {
        throw new Error(`Error code: ${response.status} \nMessage: ${response.statusText}`);
    }
    return response.json().then(data => data.twilioToken);
}

// authToken - result of authenticate function from previous snippet
getTwilioToken(authToken)
    .then(twilioToken => Chat.Client.create(twilioToken))
    .then(twilioChat => /* twilioChat initialized for 'alice' identity and ready for use */)
```

```swift
import TwilioChatClient
import VirgilSDK

let url = URL(string: "http://localhost:3000/twilio-jwt")!
let headers = ["Content-Type": "application/json",
               "Authorization": "Bearer " + authToken]
let request = Request(url: url, method: .get, headers: headers)

let response = try! connection.send(request)

let body = response.body!

let json = try! JSONSerialization.jsonObject(with: body,
                                             options: []) as! [String: Any]
let token = json["twilioToken"] as! String

TwilioChatClient.chatClient(withToken: token, properties: nil, delegate: nil) { result, client in
    guard let client = client, result.isSuccessful() else {
        // Error handling here
    }
}
```


## Step 3: Register Users on Virgil Cloud
User Registration on Virgil Cloud consists of generating a public-private keypair for a user, saving the private key on their device and publishing the public key on the Virgil Cloud (for other users to reference). 

To register users on Virgil Cloud you have to use `EThree.register()` method during the Sign Up flow in your application:

```javascript
// TODO: initialize

await eThree.register();
// or
eThree.register()
    .then(() => console.log('success'))
    .catch(e => console.error('error: ', e));
```

```swift
import VirgilE3Kit

// TODO: Initialize E3Kit

// Generates new keypair for the user. Saves private key to the device and 
// publishes public key to the Virgil's cloud
eThree.register { error in 
    guard error == nil else {
        // Error handling here 
    }
    // User private key loaded, ready to end-to-end encrypt!
}
```

```kotlin
//kotlin
```
There is no need to use `EThree.register()` method on Sign In flow.

## Step 4: Create a Twilio Channel 
Virgil doesn't provide you with any functionality to create or manage users' channels or messages. So, now you have to use Twilio SDK to create a [channel for users conversation](https://www.twilio.com/docs/chat/channels). 

## Step 5: Sign and Encrypt Messages 
Once you're a member of a channel, you can encrypt and send a message to it. You can find how to send messages to a channel using Twilio [here](https://www.twilio.com/docs/chat/channels#send-messages-to-a-channel).
As previously noted, we encrypt data to hide the message's real content from other parties. But a recipient also needs to be sure that a third party has not modified the message's content and that the message actually came from the expected, authorized sender. So, in addition to encrypting message data for data security, e3kit uses digital signatures to verify data integrity.

So, now you need to use Virgil E3Kit SDK to ecrypt messages and Twilio SDK to transmit these messages.

`eThree.encrypt(data, publicKeys)` signs the data with the sender's private key and encrypts the message for recipients' public keys.

The publicKeys parameter is a list of the recipients' public keys. In order to retrieve the public keys of users using their identities and generate this list, you'll need to use the `eThree.lookupPublicKeys(identities)` method.

```javascript
// channel - instance of Twilio Channel class
const membersIdentities = await channel.getMembers().then(members => members.map(member => member.identity));
const publicKeys = await e3kit.lookupPublicKeys(membersIdentities);
const encryptedMessage = await e3kit.encrypt('Hello, this message will be encrypted', publicKeys);
channel.sendMessage(encryptedMessage);
```

```swift
import VirgilE3Kit
import TwilioChatClient

// TODO: init and register user (see EThree.initialize and EThree.register)
// channel - instance of Twilio Channel class

channel.members?.members { result, membersPaginator in
    let identities = membersPaginator!.items().map { $0.identity! }
    
    // Lookup user public keys
    eThree.lookupPublicKeys(of: identities) { lookupResult, errors in
        guard errors.isEmpty else {
            // Error handling here
        }
    
        // Encrypt text using target user public keys
        let encryptedText = try! eThree.encrypt(text: "Hello, this message will be encrypted", for: lookupResult!)
    }
}
```

```kotlin
//kotlin
```

> Multiple recipients: one ciphertext. Even if a message is sent to multiple recipients, the resulting ciphertext (or encrypted data) will be a single blob/string that all users in the recipient list can decrypt.


## Step 6: Decrypt Data and Verify Signature
Now let's decrypt the data, then verify that they came from the correct, expected sender.
You can learn about receiving messages using Twilio [here](https://www.twilio.com/docs/chat/channels#get-the-most-recent-messages-from-a-channel).

`eThree.decrypt(data, publicKey)` decrypts the data using the recipient's private key (remember that the sender looked up the recipient's public key and encrypted the message so that only the recipient's corresponding private key could decrypt the message). It also verifies the authenticity of the decrypted data with the publicKey parameter, by confirming that the public key on the message signature is the public key of the expected sender.

Note that when a new recepient joins a channel, he won't be able to read the message history of the channel because it wasn't encrypted with his key.

```javascript
// message - instance of Twillio Message class
const authorPublicKey = await e3kit.lookupPublicKeys(message.author);
const decryptedMessage = await e3kit.decrypt(message.body, authorPublicKey);
```

```swift
import VirgilE3Kit

// TODO: init SDK and register users - see EThree.initialize and EThree.register
// message - instance of Twillio Message class

// Lookup user public key
eThree.lookupPublicKeys(of: [message.author!]) { lookupResult, errors in
    // Decrypt text and verify if it was really written by author
    let originText = try! eThree.decrypt(text: message.body!, from: lookupResult[message.author!]!)
}
```

```kotlin
//kotlin
```

# What's Next?

## Sign in from multiple devices

When users Sign Up at your server side, they use your Client application on their specific device (phone, laptop etc.), but further, users will probably be able to Sign In to your application from multiple devices/browsers.
Twilio Programmable Chat allows you to store message history so that users can synchronize and restore it when Signed In on multiple devices. But a user won't be able to read their messages without a private key stored on the device the user Signed Up with. Therefore, Virgil provides functionality which allows to share user's private key between multiple devices securely, so that users can encrypt and fully access their message history.

There are two ways to manage users with e3kit:
- **single-device use**: your users will only be able to access encrypted messages or data from a single device (smartphone or browser)
- **multi-device use**: your users will be able to access encrypted messages or data from multiple devices (both smartphone and browser)

> Note that different browsers on the same physical device are also considered different "devices".

To enable multi-device use in your application:

- **first**, use the `eThree.backupPrivateKey(pwd)` function to set a backup password and upload the encrypted private key to the cloud. The function must be called from the device that the user initially used to register his/her public key.

```javascript
// TODO: initialize and register user (see EThree.initialize and EThree.register)

await eThree.backupPrivateKey(keyPassword);
// or
eThree.backupPrivateKey(keyPassword)
    .then(() => console.log('success'))
    .catch(e => console.error('error: ', e));
```

```swift
import VirgilE3Kit

// TODO: init and register user (see EThree.initialize and EThree.register)

eThree.backupPrivateKey(password: keyPassword) { error in
    guard error == nil else {
        // Error handling here
    }
}
```

- **second**, on each new device use the `EThree.restorePrivateKey(pwd)` method to download the user's private key from the cloud and decrypt it:
```javascript
// TODO: initialize, register user and backup private key
// (see EThree.initialize, EThree.register and EThree.backupPrivateKey)

const hasPrivateKey = await eThree.hasPrivateKey();

if (!hasPrivateKey) await eThree.restorePrivateKey(keyPassword);
// or
eThree.hasPrivateKey().then(hasPrivateKey => {
    if (!hasPrivateKey) eThree.restorePrivateKey(keyPassword);
});
```

```swift
import VirgilE3Kit

// TODO: init, register user, backup key (see EThree.initialize, EThree.register and EThree.backupPrivateKey)
// Prerequisites: current local key should be cleaned up (see EThree.cleanUp)

// Note: To know whether private key is present on device you can use hasLocalPrivateKey() function:
if try! !hasLocalPrivateKey() {
    eThree.restorePrivateKey(password: keyPassword) { error in
        guard error == nil else {
            // Error handling here
        }
    }
}
```

If you are not sure whether the user is using the original device or a new device, you can use `eThree.hasPrivateKey()` method to check if the user's private key already exists on the device. If the user's private key is indeed present on the device, then you can encrypt/decrypt data. Otherwise use the `EThree.restorePrivateKey(pwd)` method to access the user's private key using their backup password.


>  **How strongly are my users' private keys protected in the cloud?** We know it might seem counter-intuitive to allow a private key off your users' devices. But it's actually quite safe. The function uses elliptic curve BLS12-381 (built by the zCash cryptocurrency team) to derive a 128-bit strong key from the password specified. This key is used to encrypt your user's private key before e3kit saves it in the Virgil Cloud.

## Rotate User Keys

Use this flow when a user's public key was published, but the private key has been lost (became not valid). The `EThree.rotatePrivateKey() method publishes a new public key and marks the previous public key as outdated.

```javascript
Copy
// TODO: initialize and register user (see EThree.initialize and EThree.register)

await eThree.rotatePrivateKey();
// or
eThree.rotatePrivateKey()
    .then(() => console.log('success'))
    .catch(e => console.error('error: ', e));
```

```swift
import VirgilE3Kit

// TODO: init, register user, lose key (see EThree.initialize and EThree.register)

eThree.rotatePrivateKey { error in
    guard error == nil else {
        // Error handling here
    }
}
```

> If a user loses their private key before making a backup, they won't be able to decrypt previously encrypted data.

## Change Backup Password

When user wants to change the password that was used to back up their private key, use the `EThree.changePassword(pwd)` method to re-encrypt the user's private key on their device with the new password, and then upload it to the Virgil Cloud.

```javascript
Copy
// TODO: initialize and register user (see EThree.initialize and EThree.register)

await eThree.changePassword(oldPassword, newPassword);
// or
eThree.changePassword(oldPassword, newPassword)
    .then(() => console.log('success'))
    .catch(e => console.error('error: ', e));
```

```swift
import VirgilE3Kit

// TODO: init and register user (see EThree.initialize and EThree.register)

eThree.changePassword(from: oldPwd, to: newPwd) { error in
    guard error == nil else {
        // Error handling here
    }
}
```

## Reset User Key

To delete a user's encrypted private key from Virgil Cloud, use the `EThree.resetPrivateKeyBackup(pwd)` method:
```javascript
// TODO: initialize, register user and backup private key
// (see EThree.initialize, EThree.register and EThree.backupPrivateKey)

await eThree.resetPrivateKeyBackup(keyPassword)
// or
eThree.resetPrivateKeyBackup(keyPassword)
    .then(() => console.log('success'))
    .catch(e => console.error('error: ', e));
```

```swift
import VirgilE3Kit

// TODO: init and register user (see EThree.initialize and EThree.register)

eThree.resetPrivateKeyBackup(password: keyPassword) { error in
    guard error == nil else {
        // Error handling here
    }
}
```

## Clean Up Device

To delete a user's private key from their device use the `EThree.cleanUp()` method:
```javascript
// TODO: initialize and register user (see EThree.initialize and EThree.register)

await eThree.cleanup();
// or
eThree.cleanup()
    .then(() => console.log('success'))
    .catch(e => console.error('error: ', e));
```

```swift
import VirgilE3Kit
// TODO: initialize and register user (see EThree.initialize and EThree.register)

try! eThree.cleanUp()
```

> When you want to clean up a user key be sure that you made its backup.
