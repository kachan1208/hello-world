# SAML Assertion Generator Service

## Introduction 
This file contains information about the SAML Assertion Generator Service, its purpose and the mechanism of interaction with it.

## Purpose 
Develop a SAML Assertion Generator Service that will generate a SAML assertion on Ionic Cloud from the 3rd-party infrastucture.

## Implementation and Technical Details
In this fill, we'll walk through a hypothetical service scenario involving a Ionic Cloud, Virgil Cloud and End-users (Developers).

The diagram below reflects the general architecture overview:
https://cdn.discordapp.com/attachments/426001031134969858/563369323477860362/Untitled_Diagram7.png

#### Participants
- Virgil Cloud
- Ionic Clod
- End-users (Developers)
- Hashicorp Vault - encrypted keys storage
- Virgil-Ionic High Level SDK

#### How it works

#### How it looks like from a Developer Side
- Developer navigates to Virgil Dashboard
- Creates the Ionic-Virgil type Application in Virgil infrastructure (on Virgil Dashbord)
- Gets Application Credentials 
- Navigates to Virgil-Ionic SDK and sets up it with Application credentials
- Makes a request to Virgil SAML Generator Service using SDK. Each Request requires a Virgil JWT
- Gets the SAML assertion
- Sends a request with SAML to specified Ionic Enrollment server

##### How it looks like from Virgil Side
- Virgil Infrastructure gets request from a developer to create a the Ionic-Virgil type Application and send Application Credentails for SDK configuration
- Virgil SAML Service makes a request to Ionic Cloud to create a new Enrollment Server
- Ionic Cloud Generates a new enrollment server and sends a RSA Key to Virgil to the SAML Service
- Virgil SAML Service receives the RSA Key, encrypts it with the Server Master Key and stores it into own Cassandra Database



## Potential SAML Service API
SAML assertion generator service have only one endpoint and it receive a bunch of data about user and generates SAML assertion for specified Enrollment service.

### Prerequisites 
- to have a Virgil JWT 

### POST /saml Endpoint 
The Endpoint generates a SAML assertion to enroll device
 
HTTP Request method: POST /saml
Transport protocol: HTTP 2.0(HTTPs)

**Request Info**:

```shell

POST /v1/saml HTTP/2.0
HOST: api.virgilsecurity.com
Content-type: application/json
Content-Length: <len>
Authorization: Virgil <JWT>
Accept: application/xml
```

**Request Body**:
```shell
{
    "application_id": <app_id>,
    "user_email": <user_email>,
    "user_name": <user_name>,
    "user_id": <id>,
    "issuer": <issuer>,
    "destination": <destination>,
}
```

**Response info**:
```shell

    HTTP/2.0 OK
    Location: api.virgilsecurity.com/v1/saml
    Content-type: application/xml
    Content-Length: <len>
```

**Responce Body**:
```shell
    <?xml version="1.0" encoding="UTF-8"?>
    <saml2p:Response xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Destination="-- USER NAME --" ID="-- ID --" InResponseTo="-- UUID V4 --" IssueInstant="2019-03-21T12:17:51.083Z" Version="2.0">
    <saml2:Issuer xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion">svrenrolltool</saml2:Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
        <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/TR/2001/REC-xml-c14n-20010315#WithComments" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="-- URI --">
            <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#">
                <ec:InclusiveNamespaces xmlns:ec="http://www.w3.org/2001/10/xml-exc-c14n#" PrefixList="xsd" />
            </ds:Transform>
            </ds:Transforms>
            <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
            <ds:DigestValue>-- DIGEST VALUE(SHA256) -- </ds:DigestValue>
        </ds:Reference>
        </ds:SignedInfo>
        <ds:SignatureValue></ds:SignatureValue>
    </ds:Signature>
    <saml2p:Status>
        <saml2p:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </saml2p:Status>
    <saml2:Assertion xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" ID="-- ASSERTION ID --" IssueInstant="-- DATETIME --" Version="2.0">
        <saml2:Issuer>-- ISSUER --</saml2:Issuer>
        <saml2:Subject>
        <saml2:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress">email</saml2:NameID>
        <saml2:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
            <saml2:SubjectConfirmationData InResponseTo="-- IN RESPONSE TO --" NotBefore="-- DATE BEFORE --" NotOnOrAfter="-- DATE FOR --" Recipient="-- IONIC RECIPIENT URL --" />
        </saml2:SubjectConfirmation>
        </saml2:Subject>
        <saml2:Conditions NotBefore="-- DATE BEFORE --" NotOnOrAfter="-- DATE FOR --">
        <saml2:AudienceRestriction>
            <saml2:Audience>-- USER NAME --</saml2:Audience>
        </saml2:AudienceRestriction>
        </saml2:Conditions>
        <saml2:AttributeStatement>
        <saml2:Attribute Name="email">
            <saml2:AttributeValue xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="xsd:string">-- USER EMAIL --</saml2:AttributeValue>
        </saml2:Attribute>
        </saml2:AttributeStatement>
        <saml2:AuthnStatement AuthnInstant="2019-03-21T12:17:51.117Z">
        <saml2:AuthnContext>
            <saml2:AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</saml2:AuthnContextClassRef>
        </saml2:AuthnContext>
        </saml2:AuthnStatement>
    </saml2:Assertion>
    </saml2p:Response>
```

### Roadmap 
#### Backend team:
 - Add a new application type to Developer service(internal service)
 - Integrate Developer with Ionic cloud
 - Integrate a Hashicorp Vault
 - Write a GO SDK for SAML generator service
 - Write a SAML assertion generator service
 - Add a routing on API Gateway
 - Add a configuration to Dashboard Backend
