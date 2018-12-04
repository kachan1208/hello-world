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


In this tutorial, we will help you add end-to-end encryption to your product to secure your message and user data.

<CollapsibleContent theme={CollapsibleContent.Theme.Grey} title="How Does End-to-End Encryption Work?"><HowItWork /></CollapsibleContent>

<SectionTitle>Set Up Your Backend</SectionTitle>

In this tutorial we assume that you have a basic backend server for your app. Your server must be able to create and store user records in persistent storage and provide a user authentication function, whether that's a simple username/password setup or a third-party provider like GitHub or Facebook.

## Provide your users access to Virgil Cloud

To make API calls to the Virgil Cloud, you'll need to provide the users of your app with a JWT that contains the user's `identity` which is a string that uniquely identifies each user in your application. For this tutorial we'll assume that your user records have a unique ID assigned by the database,  known as `uid`, and we will use that as their `identity`.

> You can use any string value for the user identity as long as it's unique for each user. For security reasons, avoid using fields that contain any personally identifiable information such as name or email address, especially if your product needs to comply with regulations such as HIPAA and GDPR.

JWTs must be generated on the server-side for several reasons: 1) each JWT must be signed with your Virgil _API Key_ to prove it was issued by you, 2) the clint side should not have access to your sensitive _API Key_ and 3) you need to authenticate the user making the request to get their `identity`.

In order to generate a JWT, you need the following information from your Virgil Account:

|Parameter|Description|
|---------|-----------|
|API_PRIVATE_KEY|Your API key's private key that is used to sign the JWTs.|
|API_KEY_ID|Your API key's ID that is a unique string value which identifies your account in the Virgil Cloud.|
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

On the client side we will use the `e3kit` SDK to create and store the user's private key on their device and publish the user's corresponding public key in the Virgil Cloud.

## Install e3kit

Use your package manager to download the e3kit SDK into your mobile or web project.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsInstallE3kit}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinInstallE3kit}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftInstallE3kit}</CodeSnippet>
</CodeSnippets>

<CollapsibleContent theme={CollapsibleContent.Theme.Grey} title="End-to-end encryption happens on the client-side"><E2eOnClient /></CollapsibleContent>


## Initialize e3kit

In order to interact with the Virgil Cloud, the e3kit SDK must be provided with a callback that it will call to fetch the Virgil JWT from your backend for the current user. In the code sample below, replace the `YOUR_ENDPOINT` placeholder with your backend endpoint's URL.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsInitializeE3kit}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinInitializeE3kit}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftInitializeE3kit}</CodeSnippet>
</CodeSnippets>

The `EThree.initialize()` function gets the user's JWT, checks whether a user already has a private key saved in local storage and a published public key on Virgil Cloud. The `EThree.initialize()` function must be used on SignUp and SignIn flows.
___

<SectionTitle>Register Users on Virgil Cloud</SectionTitle>

User Registration on Virgil Cloud consists of generating a public-private keypair for a user, saving the private key on their device and publishing the public key on the Virgil Cloud (for other users to reference).

To register users on Virgil Cloud you have to use `EThree.register()` method during the Sign Up flow in your application:

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsRegister}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinRegister}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftRegister}</CodeSnippet>
</CodeSnippets>

There is no need to use `EThree.register()` method on Sign In flow.

___

<SectionTitle>Sign and Encrypt Data</SectionTitle>

As previously noted, we encrypt data to hide the message's real content from other parties. But a recipient also needs to be sure that a third party has not modified the message's content and that the message actually came from the expected, authorized sender. So, in addition to encrypting message data for data security, e3kit uses digital signatures to verify data integrity.

`eThree.encrypt(data, publicKeys)` signs the data with the sender's private key and encrypts the message for recipients' public keys.

The `publicKeys` parameter is an array of all the recipients' public keys. In order to retrieve
the public keys of users using their `identities` and generate this array, you'll need to use the `eThree.lookupPublicKeys(identities)` function.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsSignEncrypt}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinSignEncrypt}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftSignEncrypt}</CodeSnippet>
</CodeSnippets>

>### **Multiple recipients: one ciphertext**
> Even if a message is sent to multiple recipients, the resulting ciphertext (or encrypted data) will be a single blob/string that all users in the recipient list can decrypt.

---

<SectionTitle>Decrypt Data and Verify Signature</SectionTitle>

Now let's decrypt the data, then verify that they came from the correct, expected sender.

`eThree.decrypt(data, publicKey)` decrypts the data using the recipient's private key (remember that the sender looked up the recipient's public key and encrypted the message so that only the recipient's corresponding private key could decrypt the message). It also verifies the authenticity of the decrypted data with
the `publicKey` parameter, by confirming that the public key on the message signature is the public key of the expected sender.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsDecryptVerify}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinDecryptVerify}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftDecryptVerify}</CodeSnippet>
</CodeSnippets>

___

<SectionTitle>What's next?</SectionTitle>

## Sign in from multiple devices

When you're setting up e3kit, you'll need to decide if you'll support single- or multi-device use:

- **single-device use**: your users will only be able to access encrypted messages or data from a single device (smartphone or browser)
- **multi-device use**: your users will be able to access encrypted messages or data from multiple devices (both smartphone and browser)

> Note that different browsers on the same physical device are also considered different "devices"

To enable  **multi-device use** in your application:

- **first**, use the `eThree.backupPrivateKey(pwd)` function to set a **backup password** and upload the encrypted private key to the cloud. The function must be called from the device that the user initially used to register his/her public key.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsBackup}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinBackup}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftBackup}</CodeSnippet>
</CodeSnippets>


- **second**, on each new device use the `EThree.restorePrivateKey(pwd)` method to download the user's private key from the cloud and decrypt it:

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsRestore}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinRestore}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftRestore}</CodeSnippet>
</CodeSnippets>

If you are not sure whether the user is using the original device or a new device, you can use `eThree.hasPrivateKey()` method to check if the user's private key already exists on the device. If the user's private key is indeed present on the device, then you can encrypt/decrypt data. Otherwise use the `EThree.restorePrivateKey(pwd)` method to access the user's private key using their backup password.

>### **How strongly are my users' private keys protected in the cloud?**
>We know it might seem counter-intuitive to allow a private key off your users' devices. But it's actually quite safe. The function uses elliptic curve BLS12-381 (built by the zCash cryptocurrency team) to derive a 128-bit strong key from the password specified. This key is used to encrypt your user's private key before e3kit saves it in the Virgil Cloud.

## Rotate user keys

Use this flow when a user's public key was published, but the private key has been lost (became not valid). The `EThree.rotatePrivateKey()` method publishes a new public key and marks the previous public key as outdated.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsRotate}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinRotate}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftRotate}</CodeSnippet>
</CodeSnippets>

> If a user loses their private key before making a backup, they won't be able to decrypt previously encrypted data.

## Change backup password

When user wants to change the password that was used to back up their private key, use the `EThree.resetPrivateKeyBackup(pwd)` method to re-encrypt the user's private key on their device with the new password, and then upload it to the Virgil Cloud.

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsChangePass}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinChangePass}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftChangePass}</CodeSnippet>
</CodeSnippets>

## Reset user key

To delete a user's encrypted private key from Virgil Cloud, use the `EThree.resetPrivateKeyBackup(pwd)` method:

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsReset}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinReset}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftReset}</CodeSnippet>
</CodeSnippets>

## Clean up device

To delete user's private key from their device use the `EThree.cleanUp()` method:

<CodeSnippets activeLanguage={SnippetLanguage.js}>
	<CodeSnippet language={SnippetLanguage.js} title="JavaScript" >{JsCleanUp}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.kotlin} title="Kotlin">{KotlinCleanUp}</CodeSnippet>
	<CodeSnippet language={SnippetLanguage.swift} title="Swift">{SwiftCleanUp}</CodeSnippet>
</CodeSnippets>
