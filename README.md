import { SectionTitle } from 'components/ImplementationGuide/MdxComponents.tsx'
import { CodeSnippet, CodeSnippets, SnippetLanguage } from 'components/CodeSnippets';
import HowItWork from './Collapses/hows_it_work.mdx';
import E2eOnClient from './Collapses/e2e_on_client.mdx';
import CollapsibleContent from 'components/CollapsibleContent';

import CsInstallSdk from './Code_snippets/Install_sdk/cs_install_sdk.txt';
import CsConfigureSdk from './Code_snippets/Install_sdk/cs_configure_sdk.txt';
import NodejsInstallSdk from './Code_snippets/Install_sdk/nodejs_install_sdk.txt';
import NodejsConfigureSdk from './Code_snippets/Install_sdk/nodejs_configure_sdk.txt';
import JavaInstallSdk from './Code_snippets/Install_sdk/java_install_sdk.txt';
import JavaConfigureSdk from './Code_snippets/Install_sdk/java_configure_sdk.txt';
import GoInstallSdk from './Code_snippets/Install_sdk/go_install_sdk.txt';
import GoConfigureSdk from './Code_snippets/Install_sdk/go_configure_sdk.txt';
import PhpInstallSdk from './Code_snippets/Install_sdk/php_install_sdk.txt';
import PhpConfigureSdk from './Code_snippets/Install_sdk/php_configure_sdk.txt';

import JsInstallE3kit from './Code_snippets/Install_e3kit/js_install_e3kit.txt';
import SwiftInstallE3kit from './Code_snippets/Install_e3kit/swift_install_e3kit.txt';
import KotlinInstallE3kit from './Code_snippets/Install_e3kit/kotlin_install_e3kit.txt';
import JsInitializeE3kit from './Code_snippets/Install_e3kit/js_initialize_e3kit.txt';
import SwiftInitializeE3kit from './Code_snippets/Install_e3kit/swift_initialize_e3kit.txt';
import KotlinInitializeE3kit from './Code_snippets/Install_e3kit/kotlin_initialize_e3kit.txt';

import JsSignEncrypt from './Code_snippets/Encrypt/js_sign_encrypt.txt';
import SwiftSignEncrypt from './Code_snippets/Encrypt/swift_sign_encrypt.txt';
import KotlinSignEncrypt from './Code_snippets/Encrypt/kotlin_sign_encrypt.txt';
import JsDecryptVerify from './Code_snippets/Decrypt/js_decrypt_verify.txt';
import SwiftDecryptVerify from './Code_snippets/Decrypt/swift_decrypt_verify.txt';
import KotlinDecryptVerify from './Code_snippets/Decrypt/kotlin_decrypt_verify.txt';

import JsRegister from './Code_snippets/Register/js_register.txt';
import SwiftRegister from './Code_snippets/Register/swift_register.txt';
import KotlinRegister from './Code_snippets/Register/kotlin_register.txt';

import JsBackup from './Code_snippets/Backup/js_backup.txt';
import SwiftBackup from './Code_snippets/Backup/swift_backup.txt';
import KotlinBackup from './Code_snippets/Backup/kotlin_backup.txt';

import JsRestore from './Code_snippets/Restore/js_restore.txt';
import SwiftRestore from './Code_snippets/Restore/swift_restore.txt';
import KotlinRestore from './Code_snippets/Restore/kotlin_restore.txt';

import JsRotate from './Code_snippets/Rotate/js_rotate.txt';
import SwiftRotate from './Code_snippets/Rotate/swift_rotate.txt';
import KotlinRotate from './Code_snippets/Rotate/kotlin_rotate.txt';

import JsChangePass from './Code_snippets/Change_pass/js_change_pass.txt';
import SwiftChangePass from './Code_snippets/Change_pass/swift_change_pass.txt';
import KotlinChangePass from './Code_snippets/Change_pass/kotlin_change_pass.txt';

import JsReset from './Code_snippets/Reset/js_reset.txt';
import SwiftReset from './Code_snippets/Reset/swift_reset.txt';
import KotlinReset from './Code_snippets/Reset/kotlin_reset.txt';

import JsCleanUp from './Code_snippets/Clean_up/js_clean_up.txt';
import SwiftCleanUp from './Code_snippets/Clean_up/swift_clean_up.txt';
import KotlinCleanUp from './Code_snippets/Clean_up/kotlin_clean_up.txt';


In this tutorial, we will help you add end-to-end encryption to your product in order to secure messages and user data.

<CollapsibleContent theme={CollapsibleContent.Theme.Grey} title="How Does End-to-End Encryption Work?"><HowItWork /></CollapsibleContent>

<SectionTitle>Set Up Your Backend</SectionTitle>

In this tutorial we assume that you have a basic backend server for your app. Your server must be able to create and store user records in
persistent storage and provide a user authentication strategy. It doesn't matter which authentication strategy you use, it can be simple
username/password or some third-party provider like GitHub or Facebook.

## Provide your users access to Virgil Cloud

To make API calls to the Virgil Cloud, the users of your app will need a JWT, which you'll need to provide them with.
The JWT must include the user's `identity` which is a string that uniquely identifies each user in your application.
For this tutorial we'll assume that your user records have a unique ID assigned by the database,  known as `uid`, and we will use that as their `identity`.

> You can use any string value for the user identity as long as it's unique for each user. For security reasons, avoid using fields that contain any personally identifiable information such as name or email address.

Each JWT must be signed with your Virgil _API Key_ to prove that it was issued by you. Therefore the generation of JWTs must be done
server-side, because you need to authenticate the user making the request to get their `identity`, and also, because your Virgil API Key is sensitive information,
which the client side of your app must not have access to.

In order to generate a JWT, you need the following information from your Virgil Account:

|Parameter|Description|
|---------|-----------|
|API_PRIVATE_KEY|Private key of your API key that is used to sign the JWTs.|
|API_KEY_ID|ID of your API key. A unique string value that identifies your account in the Virgil Cloud.|
|APP_ID|ID of your Virgil Application.|

We will use `VirgilSDK` and `VirgilCrypto` libraries to generate and sign the JWTs on your server.

>### **JWT grants access to Virgil Cloud for a specific Virgil Application**
> Each JWT grants a user access to the Virgil Cloud for a specific Virgil Application and has a limited lifetime that is configured by you. However, best practice is to generate a JWT for the shortest amount of time feasible for your application. If you need more details about the structure of a JWT, please take a look at this [article](https://developer.virgilsecurity.com/docs/javascript/how-to/setup/v5/setup-authentication).

## Install VirgilSDK

Use your package manager to install VirgilSDK on your backend.

<CodeSnippets activeLanguage={SnippetLanguage.nodeJs}>
	<CodeSnippet language={SnippetLanguage.nodeJs}>{NodejsInstallSdk}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.php}>{PhpInstallSdk}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Java">{JavaInstallSdk}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.go}>{GoInstallSdk}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.cs}>{CsInstallSdk}</CodeSnippet>
</CodeSnippets>

## Generate JWT

To generate JWT, you will need to use the `JwtGenerator` class from the SDK.

<CodeSnippets activeLanguage={SnippetLanguage.nodeJs}>
	<CodeSnippet language={SnippetLanguage.nodeJs}>{NodejsConfigureSdk}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.php}>{PhpConfigureSdk}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Java">{JavaConfigureSdk}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.go}>{GoConfigureSdk}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.cs}>{CsConfigureSdk}</CodeSnippet>
</CodeSnippets>

Now you need to provide an HTTP endpoint which will return the JWT with the user's identity as a JSON.
___

<SectionTitle>Set Up Your Client</SectionTitle>

On the client side we will use the `e3kit` SDK for creating and storing the user's private key on their device an publish the appropriate public key
in the Virgil Cloud.

## Install e3kit

Use your package manager to download the e3kit SDK into your mobile or web project.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsInstallE3kit}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinInstallE3kit}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftInstallE3kit}</CodeSnippet>
</CodeSnippets>

<CollapsibleContent theme={CollapsibleContent.Theme.Grey} title="End-to-end encryption happens on the client-side"><E2eOnClient /></CollapsibleContent>


## Initialize e3kit

In order to interact with the Virgil Cloud, the e3kit SDK must be provided a callback that it will call to fetch the Virgil JWT for the
current user from your backend. In the code sample below, replace the `YOUR_ENDPOINT` with the URL of your backend endpoint.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsInitializeE3kit}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinInitializeE3kit}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftInitializeE3kit}</CodeSnippet>
</CodeSnippets>

The `EThree.initialize()` function gets user's JWT, checks whether a user has a private key in a local storage and a published public key on Virgil Cloud. The `EThree.initialize()` function must be used on SingUp and SignIn flows.
___

<SectionTitle>Register Users on Virgil Cloud</SectionTitle>

User Registration on Virgil Cloud is a process of generating a keypair for a user, saving the private key on their device and publishing the public key on the Virgil Cloud (for other users to reference).

To register users on Virgil Cloud you have to use `EThree.register()` method on the Sign Up flow in your application:

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsRegister}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinRegister}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftRegister}</CodeSnippet>
</CodeSnippets>

There is no need to use `EThree.register()` method on Sign In flow.

___

<SectionTitle>Sign and Encrypt Data</SectionTitle>

As previously noted, we encrypt data to hide the message's real content from other parties. But a recipient also needs to be sure that a third party has
not modified the message's content and that the message actually came from the correct sender. So, in addition to encrypting message data for data security,
e3kit uses digital signatures to verify data integrity.

`eThree.encrypt(data, publicKeys)` signs the data with the sender's private key and encrypts them for recipients' public keys.

The `publicKeys` parameter is an array of public keys of the recipients. You need to use the `eThree.lookupPublicKeys(identities)` method to retrieve
the public keys of users by their `identities`.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsSignEncrypt}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinSignEncrypt}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftSignEncrypt}</CodeSnippet>
</CodeSnippets>

>### **Multiple recipients: one ciphertext**
> Even though multiple recipients are specified, the resulting ciphertext will be a single blob/string that all users in the recipient list can decrypt.

---

<SectionTitle>Decrypt Data and Verify Signature</SectionTitle>

Now let's decrypt the data, then verify that they came from the claimed sender.

`eThree.decrypt(data, publicKey)` decrypts the data using the current user's private key. It also verifies the authenticity of the decrypted data with
the `publicKey` parameter, which must be the public key of the sender of the message.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsDecryptVerify}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinDecryptVerify}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftDecryptVerify}</CodeSnippet>
</CodeSnippets>

___

<SectionTitle>What's next?</SectionTitle>

## Sign In from multiple devices

When users Sign Up at your server side, they use your client-application on their specific device (phone, laptop etc.), but further, probably users will be able to Sign In to your application from multiple devices/browsers.

There are two ways to manage users with e3kit:

- **single-device use**: your users will only be able to access encrypted messages or data from a single device (smartphone or browser)
- **multi-device use**: your users will be able to access encrypted messages or data from multiple devices (both smartphone and browser)

> Note that different browsers on the same physical device are also considered different "devices"

To enable  **multi-device use** in your application:

- **first**, use the `eThree.backupPrivateKey(pwd)` function to set a **backup password** and upload the encrypted private key to the cloud. The function must be called from the device that the user used to register his/her public key.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsBackup}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinBackup}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftBackup}</CodeSnippet>
</CodeSnippets>


- **second**, on each new device use the `EThree.restorePrivateKey(pwd)` method to download the user private key from the cloud and decrypt it:

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsRestore}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinRestore}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftRestore}</CodeSnippet>
</CodeSnippets>

If you are not sure whether the user is using a new device or not you can use `eThree.hasPrivateKey()` method to check if user's private key is present on this device. If user's private key is present at the device, then you can encrypt/decrypt data; otherwise use the `EThree.restorePrivateKey(pwd)` method to restore user's private key with their backup password.

>### **How strongly are my users' private keys protected in the cloud?**
>We know it might seem counter-intuitive to allow a private key off your users' devices. But it's actually quite safe. The function uses elliptic curve BLS12-381 (built by the zCash cryptocurrency team) to derive a 128-bit strong key from the password specified. This key is used to encrypt your user's private key before e3kit saves it in the Virgil Cloud.

## Rotate User Keys

Use this flow when a user's public key was published, but the private key has been lost (became not valid). The `EThree.rotatePrivateKey()` method publishes a new public key and marks the previous public key as outdated.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsRotate}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinRotate}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftRotate}</CodeSnippet>
</CodeSnippets>

> If a user loses their private key before making a backup, they won't be able to decrypt previously encrypted data.

## Change Backup Password

When user wants to change their password that was used to back up a private key, use the `EThree.resetPrivateKeyBackup(pwd)` method to do it. The `EThree.resetPrivateKeyBackup(pwd)` re-encrypts user's private key on a device and uploads it to the Virgil Cloud.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsChangePass}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinChangePass}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftChangePass}</CodeSnippet>
</CodeSnippets>

## Reset User Key

To delete user's encrypted private key from Virgil Cloud use the `EThree.resetPrivateKeyBackup(pwd)` method:

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsReset}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinReset}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftReset}</CodeSnippet>
</CodeSnippets>

## Clean Up Device

To delete user's private key from their device use the `EThree.cleanUp()` method:

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsCleanUp}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinCleanUp}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftCleanUp}</CodeSnippet>
</CodeSnippets>
