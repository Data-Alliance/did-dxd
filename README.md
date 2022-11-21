# Data-Alliance Decentralized IDs Method Specification

## Introduction
DA소개 및 연락처
DID표준 버전
## Overview
The system aims to provide secure authentication and various services based on DID & Verifiable Credential Specifications published by the W3C.

### Method Name
The DA-DID(Data-Alliance DID) Method name is **dxd**
A DA-DID that uses this method **MUST** begin with the prefix "did:dxd", this prefix **MUST** be in lowercase.

## Specification
### Generating a Key-Pair
It is generated using the elliptic curve encryption algorithm(secp256k1) using Neal Koblitz elliptic curve. (See http://www.secg.org/sec2-v2.pdf)   
Key generation must be generated directly from the device of the DID Subject.

### Generating a unique idstring
   
DA-DID format is descibed below.

![did:dxd_format](https://github.com/Data-Alliance/did-dxd/blob/master/did:dxd_format.png)

### DID Document Model
DA-DID (Data-Alliance DIDs) provides a DID Document that are mapped to the DID of registered Holder through Verifiable Data Registry.   
The DID Document does not contain any kind of "personal information" that can identify the holder, and the service provided through DA-DID
It contains only information for proof of identity and proof of authority.   
Under DA-DID, the transfer of personal information must be done only through an encrypted Verifiable Credential.   

![did-document_format1](https://github.com/Data-Alliance/did-dxd/blob/master/did-document_format1.png)
![did-document_format2](https://github.com/Data-Alliance/did-dxd/blob/master/did-document_format2.png)

## CRUD Operation
### DID Enroll (Create)
Clients (Holder, Issuer, Verifier) must directly create a Key-Pair to be used for DID registration and create a “specific id string” based on the generated key.
The DID and Public-Key generated in this way become the payload of the request message, and the request is delivered to the issuer.   
* DID Enroll Request : Client to Issuer
<pre>
{
  “id” : “did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87”,
  “publicKeyBase58” : “49PpU4vgqFNtNUAeaeR2m6ZLsgMavL54HtXaTwYdqyi7xKQXREUw219nEH”
}
</pre>
Upon receiving the client's request message, the issuer adds "controller" and necessary "service" information on request message and convey it to Data-Alliance's Verifiable Data Registry.
After conveying, the DID Document for the request is created in the Data-Alliance Verifiable Data Registry and finally delivered to the Client through the Issuer.
* DID Enroll Request : Issuer to Data-Alliance Verifiable Data Registry
<pre>
{
  “id” : “did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87”,
  “publicKeyBase58” : “49PpU4vgqFNtNUAeaeR2m6ZLsgMavL54HtXaTwYdqyi7xKQXREUw219nEH”
  "controller": "did:dxd:0000da235c7923cac3a7af330ade1cdaf21a657a7cca8c8bcfa7",
  "service": [
    {
      "id": "did:dxd:0000da235c7923cac3a7af330ade1cdaf21a657a7cca8c8bcfa7#svc-did",
      "type": "DidControl",
      "serviceEndpoint": "https://did.example.co.kr"
    }
  ]
}
</pre>


### DID Infomation (Read)
### DID Update (Update)
#### Public-Key Add & Update
#### Deactivate & Enactivate
### DID Rovoke (Delete)
## References
- Decentralized Identifiers (DIDs) v1.0 https://www.w3.org/TR/2022/REC-did-core-20220719/
- DID Specification Registries https://www.w3.org/TR/2022/NOTE-did-spec-registries-20220809/
- JSON-LD 1.0 - A JSON-based Serialization for Linked Data https://www.w3.org/TR/json-ld
