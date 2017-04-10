# Virgil CLI

[Installation](#installation) | [Commands](#commands) | [Using Example](#using-example) | [Support](#support)

[Virgil Security](https://virgilsecurity.com) is a stack of security libraries and all the necessary infrastructure to enable seamless, end-to-end encryption for any application, platform or device. The Command-line interface (CLI) program is a command line tool for utilizing [Virgil Services](https://developer.virgilsecurity.com/docs/java/references). In a few simple steps you can encrypt and decrypt, sign and verify.


For a full overview head over to our [developer portal](https://developer.virgilsecurity.com/docs/java/references/utilities/cli)

## Installation

The Virgil CLI is provided as a script and an archive of the files, and it is available for Mac and Linux OS.

In order to use the CLI:
1. download the CLI archive or script for you platform [here](/);
2. install the CLI with:
  - MAC OS [install guide](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/settings/install/macos);
  - Linux OS [install guide](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/settings/install/linux).

## Commands

Using the CLI you can:
  * [Generate a Virgil Key](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/generate-key) (Private Key)
  * [Extract a Public Key](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/public-key)
  * [Create](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/create-card) a User's Virgil Card
  * [Get](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/get-card) a User's Virgil Card
  * [Search](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/search-card) for a User's Virgil Card
  * [Revoke](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/revoke-card) a User's Virgil Card
  * [Encrypt data](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/encrypt)
  * [Decrypt](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/decrypt) the encrypted data
  * [Sign data](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/sign)
  * [Verify](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/verify) the signature
  * Some additional commands:
    * Change [Key format](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/additional-commands/key-format)
    * See use's [Card info](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/additional-commands/card-info) (content)
    * Use [Secret Alias](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/additional-commands/secret-alias)

[Learn more about the CLI commands](https://developer.virgilsecurity.com/docs/java/references/utilities/cli) in our documentation.

## Using Example

Virgil Security makes it very easy to encrypt anything in a minutes. With our CLI you need only one line of the code and you will get an encrypted data for every one of your users and devices.

Example: Alice encrypts some 'plain.txt' for Bob using his email as a recipient-id:

```bash
virgil encrypt -i plain.txt -o enc.txt email:bob@mail.com
```
This will result in the creation of a newly encrypted file (enc.txt).  In this example, the encrypted file will be stored in the folder, from which the command was run.


[Learn more about our the different ways of encryption](https://developer.virgilsecurity.com/docs/java/references/utilities/cli/commands/encrypt) with the CLI in our documentation.


## License

BSD 3-Clause. See [LICENSE](https://github.com/VirgilSecurity/virgil/blob/master/LICENSE) for details..

## Support

Our developer support team is here to help you. You can find us on [Twitter](https://twitter.com/virgilsecurity) and [email](mailto:support@virgilsecurity.com).
