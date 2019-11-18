# Aries RFC 0312: Crypto service Protocol 1.0

- Authors: Robert Mitwicki
- Status: [PROPOSED](/README.md#proposed)
- Since: 2019-11-20 (date you submit your PR)
- Status Note: RFC under development
- Supersedes:
- Start Date: 2019-11-01
- Tags: feature, protocol

## Summary

Within decentralized data economy with user-centric approach user is the one who should control data flows and all interaction even on 3rd parties platforms. To achieve that we starting talking about access instead of ownership of the data. Within the space we can identify some services which are dealing with your data but they don't necessarily need to be able to see the data. In this category we have services like archive, data vaults, data transportation (IM, Email, Sent file), etc. To be able to better support privacy in such cases this document proposes a protocol which uses underlying security mechanisms within agents like `Lox` to provide API for cryptographic operation like encryption, decryption, signature, verification, proxy re-encryption to let those services to provide additional security layer on their platform within connection to SSI wallet agents.

## Motivation

Identity management and key management are complex topics with which even big players having problems. To help companies and their products build secure and privacy preserving services with SSI they need a simple mechanism to get access to the cryptographic operation within components of the wallets.

Many services provides solutions like secure storage, encryption communication, secure data transportation and to achieve that they are using secure keys to provide cryptography for their use cases. The problem is that in many cases those keys are generated within their services which reduce the trust and increase the risk of leaking the key.

The ideal solution is that the keys are generated within user agent and never leaves that place.

Providing general crypto API to the user wallet allows to support generic use cases where cryptography layer is required, for example :

- encrypted data in data vaults
- encrypted messages within 3rd party service
- additional security layers outside of Agent ecosystem, data transportation, messaging over non-DIDComm protocols.
- backup/restore systems

Desired outcome would be to have Agent which is able to expose crypto API to external services.

## Tutorial

### Name and Version

This defines the `crypto-service` protocol. version 0.1, as identified by the following PIURI:

    TODO: Add PIURI when ready

### Roles

The minimum amount of roles involved in the `crypto-service` protocol are: `sender` and `receiver`. The `sender` request certian operation from the `receiver` and `receiver` provides result in a form of payload or an error.
Protocl could incolde more roles which could be involved in process of proxy re-encryption, delegation etc.

### Constraints

Each message which is sent to the agent required up front established relationship between sender and receiver in a form of authorization. Means that the sender is allow to use only this specific key which is meant for him. There should not be the case where the sender is able to trigger any operation with keys which where never used within his service.

## Reference

### Examples

Specific use case example:

Platform providing secure document transportation between parties and archiving functionality.

Actors:

- `Sender`: User sending document
- `Receiver`: User receiving document
- `DocuArch` - Platform providing secure document transportation and archiving.
- `DocuArchApp` - client side application allowing to display documents

Here is how it could work:

- `Receiver` register to the services with his DID identity
- `Receiver` share his DID with the `Sender`
- `Sender` knowing DID of the `Receiver` encrypt the document with `Receiver` public key and sends document on the platform.
- `DocuArch` securely transport that document to the `Receiver` at the same time archiving it for the `Sender` and `Receiver`.
- `Receiver` is informed by the `DocuArchApp` that there is a new document to view.
- `Receiver` opens encrypted payload within `DocuArchApp`
- `DocuArchApp` send request to the user Agent to decrypt the message for the `Receiver`
- Agent decrypt the message and sent back decrypted payload
- `DocuArch` displays decrypted payload within local app.

In this scenario `DocuArch` has no way to learn about what is in the payload which is sent between `Sender` and `Receiver` as the only person who is in possession of the private key is able to decrypt the payload is `Receiver`
Decrypted payload never leaves `Receiver` client app.

Such feature within Agent allow companies to build faster and more secure systems as the identity management and key management part comes from Agents and they just interact with it via API.

### Messages

Protocol: did:sov:1234;spec/crypto-service/1.0

**encrypt**

- payload - raw data which should be encrypted
- key_id - reference to the specific did key which was used to establish connection with the service.

```
    {
        "@id": "1234567889",
        "@type": "did:sov:1234;spec/crypto-service/1.0/encrypt",
        "payload": "Text to be encrypted"
        "key_id": "did:example:123456789abcdefghi#keys-1

    }
```

**decrypt**

- encryptedPayload - encrypted payload to be decrypt
- key_id - reference to the specific did key which was used to establish connection with the service.

```
    {
        "@id": "1234567889",
        "@type": "did:sov:1234;spec/crypto-service/1.0/decrypt",
        "encryptedPayload": "ASDD@J(!@DJ!DASD!@F"
        "key_id": "did:example:123456789abcdefghi#keys-1

    }
```

**sign**

- payload - payload to be signed
- key_id - reference to the specific did key which was used to establish connection with the service.

```
    {
        "@id": "1234567889",
        "@type": "did:sov:1234;spec/crypto-service/1.0/sign",
        "payload": "I say so!"
        "key_id": "did:example:123456789abcdefghi#keys-1

    }
```

**verify**

- signature - signature to be verified
- key_id - reference to the specific did key which was used to establish connection with the service.

```
    {
        "@id": "1234567889",
        "@type": "did:sov:1234;spec/crypto-service/1.0/verify",
        "signature": "12312d8u182d812d9182d91827d179"
        "key_id": "did:example:123456789abcdefghi#keys-1

    }
```

**delegation**

TODO

**transitivity**

TODO

### Message Catalog

TODO: add error codes and response messages/statuses

### Responses

TODO

## Drawbacks

- Potentialy expose Agent for different types of attacts: e.g. someone would try to decrypt your private documents without you being notice of that.
-

## Rationale and alternatives

We can not expect that each services will switch directly to the DIDComm and other features of the agents. Not all features are even desier to have within agent. But if the Agent can exposer base API for identity management and crypto operation this would allow others to build on top of it much more richer applications and platforms.

I am not aware of any alternatives atm. Anyone?

## Prior art

Similar approach is taken within HSM world where API is expose to the outside world without exposing keys. Here we take same approach in the context of KMS within Agent.

## Unresolved questions

- How to check authorization that this service is allow to use those set of the keys?
- How to protect user against malicious requests?
- What other operation should be supported?
- What are the limitation in the context of payload?

## Implementations

_Implementation Notes_

| Name / Link | Implementation Notes |
| ----------- | -------------------- |
|             |
