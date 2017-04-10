# Virgil CLI

[Installation](#installation) | [Using Example](#using-example) | [Commands](#commands) | [Support](#support)

[Virgil Security](https://virgilsecurity.com) provides a set of APIs for adding security to any application. The Command-line interface (CLI) program is a command line tool for utilizing [Virgil Services](https://developer.virgilsecurity.com/docs/java/references). In a few simple steps you can encrypt and decrypt, sign and verify.


For a full overview head over to our [developer portal](https://developer.virgilsecurity.com/docs/java/references/utilities/cli)

## Installation

The Virgil CLI is provided as a script and an archive of the files, and it is available for Mac and Linux OS.

In order to use the CLI:
1. download the CLI archive or script for you platform [here](/);
2. install the CLI with:
  - MAC OS [install guide](/);
  - Linux OS [install guide](/).

## Commands

Using the CLI you can:
  * [Generate a Virgil Key](/references/utilities/cli/commands/generate-key) (Private Key)
  * [Extract a Public Key](/references/utilities/cli/commands/public-key)
  * [Create](/references/utilities/cli/commands/create-card) a User's Virgil Card
  * [Get](/references/utilities/cli/commands/get-card) a User's Virgil Card
  * [Search](/references/utilities/cli/commands/search-card) for a User's Virgil Card
  * [Revoke](/references/utilities/cli/commands/revoke-card) a User's Virgil Card
  * [Encrypt data](/references/utilities/cli/commands/encrypt)
  * [Decrypt](/references/utilities/cli/commands/decrypt) the encrypted data
  * [Sign data](/references/utilities/cli/commands/sign)
  * [Verify](/references/utilities/cli/commands/verify) the signature
  * Some additional commands:
    * Change [Key format](/references/utilities/cli/commands/additional-commands/key-format)
    * See use's [Card info](/references/utilities/cli/commands/additional-commands/card-info) (content)
    * Use [Secret Alias](/references/utilities/cli/commands/additional-commands/secret-alias)

[Learn more about the CLI commands](/) in our documentation.

## Using Example

Virgil Security makes it very easy to encrypt anything in a minutes. With our CLI you need only one line of the code and you will get an encrypted data for every one of your users and devices.

Example: Alice encrypts some 'plain.txt' for Bob using his email as a recipient-id:

```bash
virgil encrypt -i plain.txt -o enc.txt email:bob@mail.com
```
This will result in the creation of a newly encrypted file (enc.txt).  In this example, the encrypted file will be stored in the folder, from which the command was run.


[Learn more about our the different ways of encryption](/) with the CLI in our documentation.


## License

BSD 3-Clause. See [LICENSE](https://github.com/VirgilSecurity/virgil/blob/master/LICENSE) for details..

## Support

Our developer support team is here to help you. You can find us on [Twitter](https://twitter.com/virgilsecurity) and [email](mailto:support@virgilsecurity.com).
