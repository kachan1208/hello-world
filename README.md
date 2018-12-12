# Search for Cards With A User's identity

This guide shows how to search for a user's Cards on [Virgil Cards Service](/api-reference/card-service/v5) by their `identity`.

<Info>Remember that each user can have multiple Cards attached to their identity.</Info>

## Prerequisites
<Collapse title="### Install SDK & Setup Virgil Crypto">
#{ import "/how_to/setup/v5/__code__/install_sdk/%lang%#install_sdk" }
</Collapse>

<Collapse title="### Setup authentication on the client side and send a JWT request">
To make API calls to the Virgil Cloud, you'll need to provide your product's users with a JSON Web Token ("JWT") that contains the user's identity, made up of a string that uniquely identifies each user in your application.

Virgil SDK allows you to use the Virgil token-generation functionality to generate a JWT. After you install the Virgil SDK package on the client side, you'll need to set up a `jwtProvider` to provide users with a unique JWT.

#### Set up JWT provider
use this code to choose what you'll use for JWT generation in your product:

#{ import "/how_to/setup/v5/__code__/authentication_jwt/%lang%#setup_jwt" }
</Collapse>

<Collapse title="### Set up authentication on the server side and generate a JWT">


**Collect your Virgil developer credentials**

<table class="params">
<thead>
  <tr>
    <th>Parameter</th>
    <th>Description</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>APP_ID</td>
    <td>ID of your Application at [Virgil Dashboard](https://dashboard.virgilsecurity.com)</td>
  </tr>

  <tr>
    <td>API_KEY_ID</td>
    <td>A unique string value that identifies your account on the Virgil developer portal</td>
  </tr>

  <tr>
    <td>API_KEY</td>
    <td>A Private Key that is used to sign API calls to Virgil Services. For security reasons, you will only be shown the API Private Key right when the key is created, so don't forget to save it in a secure location for the next step</td>
  </tr>

</tbody>
</table>

Next, you'll set up the `JwtGenerator` using the Virgil SDK.

<Info>  You'll use your API Key that was created on the Virgil Dashboard. that's why, for security, you have to generate JWT on your server side. </Info>

Each JWT grants a user access to the Virgil Cloud for a specific Virgil Application and has a limited lifetime that is configured by you. However, best practice is to generate a JWT for the shortest amount of time feasible for your application.

Here is an example of how to generate a JWT:

#{ import "/how_to/setup/v5/__code__/authentication_jwt/%lang%#jwt_generation" }
</Collapse>

<Collapse title="### Set up Card Verifier">

`VirgilCardVerifier` helps you automatically verify a Card's signatures, which you'll want to do when receiving messages and performing most other functions that involve checking a sender or recipient's identity.

By default, `VirgilCardVerifier` verifies only two signatures - that of the Card owner and of Virgil Cards Service.

Set up `VirgilCardVerifier` with the following lines of code:
#{ import "/how_to/setup/v5/__code__/setup_card_verifier/%lang%#setup_card_verifier" }
</Collapse>

<Collapse title="### Set up Cards Manager">

 With Virgil Cards Manager you can:
- specify a JWT Provider
- specify a Crypto Library that you’re planning to use for crypto operations
- specify a Card Verifier that is used to verify the signatures on a user's Card

### Set up Crypto Library
Use the following lines of code to specify which crypto library (Virgil's or your own) you prefer to use in your project:

#{ import "/how_to/setup/v5/__code__/setup_card_manager/%lang%#setup_crypto" }

## Initialize Card Manager

Use the following lines of code to set up the Card Manager:

#{ import "/how_to/setup/v5/__code__/setup_card_manager/%lang%#initialize_cardmanager" }

</Collapse>

## Search Users Cards
Using Virgil SDK, you can search for the Cards of a single user or multiple users.

Search for a single user's Cards:

#{ import "/how_to/public_key_management/v5/__code__/%lang%#search_card_by_identity" }


Search for multiple users' Cards:

#{ import "/how_to/public_key_management/v5/__code__/%lang%#search_card_by_identity" }

