# Data-Alliance Decentralized IDs Method Specification

## About Data-Alliance DIDs
The system aims to provide secure authentication and various services based on DID & Verifiable Credential Specifications published by the W3C.   
As an identity verification technology, Data-Alliance DIDs are designed to use blockchain technology to prove one's own identity without a separate intermediary, making it impossible to forge and alter.   
Necessary personal information is stored in a safe area (Trusted Execution Environment) that only individuals can access, and is configured so that it can be submitted when necessary to prove authority.   
In addition, when Data-Alliance DIDs are assigned to various IoT devices, it is possible to verify the authenticity of data without the risk of forgery and alteration during data collection and transmission.   
This document conforms to the requirements specified in Decentralized Identifiers (DIDs) v1.0 (See. https://www.w3.org/TR/2022/REC-did-core-20220719/)

### Method Name
DA-DID(Data-Alliance DIDs) Method name is **dxd**.   
A DA-DID that uses this method **MUST** begin with the prefix **"did:dxd:"**, this prefix **MUST** be in lowercase.

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
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/secp256k1-2019/v1"
  ],
  "id": "<Holder.DID>",
  "controller": "<Issuer.DID>",
  "created": "2022-03-05T11:40:27+09:00",
  "updated": "",
  "deactivated": false,
  "authentication": [
    "<Holder.DID>#keys-1"
  ],
  "assertionMethod": [
    "<Holder.DID>#keys-1"
  ],
  "keyAgreement": [
    "<Holder.DID>#keys-1"
  ],
  "verificationMethod": [
    {
      "id": "<Holder.DID>#keys-1",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "<Holder.DID>",
      "publicKeyBase58": "zEY59y7px76e2yv5FMj9fYcjDsqk8yus6isWtkF69ZrHY"
    }
  ],
  "service": [
    {
      "id": "<Issuer.DID>#svc-did",
      "type": "DidControl",
      "serviceEndpoint": "https://did.example.co.kr"
    },
    {
      "id": "<Issuer.DID>#svc-citizenship",
      "type": "CitizenShip",
      "serviceEndpoint": "https://citizen.example.co.kr"
    },
    {
      "id": "<Issuer.DID>#svc-iot-lora",
      "type": "IotLoRa",
      "serviceEndpoint": "https://lora.example.co.kr"
    },
    {
      "id": "<Issuer.DID>#svc-iot-wifi",
      "type": "IotWifi",
      "serviceEndpoint": "https://wifi.example.co.kr"
    }
  ],
}
```

## CRUD Operation
### DID Enroll (Create)
Clients (Holder, Issuer, Verifier) must directly create a Key-Pair to be used for DID registration and create a “specific id string” based on the generated key.
The DID and Public-Key generated in this way become the payload of the request message, and the request is delivered to the issuer.   
* DID Enroll Request : Holder to Issuer
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1"
  ],
  "id": "<Holder.DID>",
  "verificationMethod": [
    {
      "id": "<Holder.DID>#keys-1",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "<Holder.DID>",
      "publicKeyBase58": "49PpU4vgqFNtNUAeaeR2m6ZLsgMavL54HtXaTwYdqyi7xKQXREUw219nEH"
    }
  ]
}
```

Upon receiving the client's request message, the issuer adds "controller" and necessary "service" information on request message and convey it to Data-Alliance's Verifiable Data Registry.
After conveying, the DID Document for the request is created in the Data-Alliance Verifiable Data Registry and finally delivered to the Client through the Issuer.
* DID Enroll Request : Issuer to Data-Alliance Verifiable Data Registry
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1"
  ],
  "credentialSubject": {
    "id": "<Holder.DID>",
    "controller": "<Issuer.DID>",
    "verificationMethod": [
      {
        "id": "<Holder.DID>#keys-1",
        "type": "EcdsaSecp256k1VerificationKey2019",
        "controller": "<Holder.DID>",
        "publicKeyBase58": "49PpU4vgqFNtNUAeaeR2m6ZLsgMavL54HtXaTwYdqyi7xKQXREUw219nEH"
      }
    ],
    "service": [
      {
        "id": "<Issuer.DID>#svc-did",
        "type": "DidControl",
        "serviceEndpoint": "https://did.example.co.kr"
      },
      {
        "id": "<Issuer.DID>#svc-citizenship",
        "type": "CitizenShip",
        "serviceEndpoint": "https://citizen.example.co.kr"
      }
    ]
  },
  "proof": {
    "type": "EcdsaSecp256k1VerificationKey2019",
    "created": "2022-03-05T18:19:39Z",
    "verificationMethod": "<Issuer.DID>#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
  }
}
```

* DID Enroll Response : Data-Alliance Verifiable Data Registry -> Issuer -> Holder
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/secp256k1-2019/v1"
  ],
  "id": "<Holder.DID>",
  "controller": "<Issuer.DID>",
  "created": "2022-03-05T11:40:27+09:00",
  "updated": "",
  "deactivated": false,
  "authentication": [
    "<Holder.DID>#keys-1"
  ],
  "assertionMethod": [
    "<Holder.DID>#keys-1"
  ],
  "keyAgreement": [
    "<Holder.DID>#keys-1"
  ],
  "verificationMethod": [
    {
      "id": "<Holder.DID>#keys-1",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "<Holder.DID>",
      "publicKeyBase58": "49PpU4vgqFNtNUAeaeR2m6ZLsgMavL54HtXaTwYdqyi7xKQXREUw219nEH"
    }
  ],
  "service": [
    {
      "id": "<Issuer.DID>#svc-did",
      "type": "DidControl",
      "serviceEndpoint": "https://did.example.co.kr"
    },
    {
      "id": "<Issuer.DID>#svc-citizenship",
      "type": "CitizenShip",
      "serviceEndpoint": "https://citizen.example.co.kr"
    }
  ],
}
```

### DID Infomation (Read)

Clients can get information of a specific DID by sending it to the Data-Alliance Verifiable Data Registry.   
After Requesting, a DID Document would return 
* DID Infomation Request : Client to Data-Alliance Verifiable Data Registry
<pre>
{
  "@context": [
    "https://www.w3.org/ns/did/v1"
  ],
  "id" : "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87"
}
</pre>

### DID Update (Update)
DID Document can be updated.   
The request message must include items for updating in "credentialSubject" and proof (electronic signature) to prove it.   
Update items can be public-key, deactivate and service.   
Depending on each item, the required proof is slightly different.

#### Public-Key Add & Update
DID Subject can change the existing public-key or add a new public-key in DID Document.   
The holder's digital signature is required for the request message.   
The public-key is defined in "verificationMethod", and if "id" already exists, it is updated, and if it does not exist, it is added.   
This "id" must be different from "proof"-"verificationMethod".   
If success, the updated DID Document would be returned   
* Request message
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1"
  ],
  "credentialSubject": {
    "id": "<Holder.DID>",
    "verificationMethod": [
      {
        "id": "<Holder.DID>#keys-2",
        "type": "EcdsaSecp256k1VerificationKey2019",
        "controller": "<Holder.DID>",
        "publicKeyBase58": "e7Pg44vgqNNtNUAa1eR26mZLLgMavv44HtTwYaXdqyi7xKxnwQRU219nw1"
      }
    ]
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2021-11-13T18:19:39Z",
    "verificationMethod": "<Holder.DID>#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
  }
}
```

* Updated DID Document
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1",
    "https://w3id.org/security/suites/secp256k1-2019/v1"
  ],
  "id": "<Holder.DID>",
  "controller": "<Issuer.DID>",
  "created": "2022-03-05T11:40:27+09:00",
  "updated": "2023-03-05T11:40:27+09:00",
  "deactivated": false,
  "authentication": [
    "<Holder.DID>#keys-1",
    "<Holder.DID>#keys-2"
  ],
  "assertionMethod": [
    "<Holder.DID>#keys-1",
    "<Holder.DID>#keys-2"
  ],
  "keyAgreement": [
    "<Holder.DID>#keys-1",
    "<Holder.DID>#keys-2"
  ],
  "verificationMethod": [
    {
      "id": "<Holder.DID>#keys-1",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "<Holder.DID>",
      "publicKeyBase58": "49PpU4vgqFNtNUAeaeR2m6ZLsgMavL54HtXaTwYdqyi7xKQXREUw219nEH"
    },
    {
      "id": "<Holder.DID>#keys-2",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "<Holder.DID>",
      "publicKeyBase58": "e7Pg44vgqNNtNUAa1eR26mZLLgMavv44HtTwYaXdqyi7xKxnwQRU219nw1"
    }
  ],
  "service": [
    {
      "id": "<Issuer.DID>#svc-did",
      "type": "DidControl",
      "serviceEndpoint": "https://did.example.co.kr"
    },
    {
      "id": "<Issuer.DID>#svc-citizenship",
      "type": "CitizenShip",
      "serviceEndpoint": "https://citizen.example.co.kr"
    }
  ],
}
```

#### Service Add
"service" can be added according to the necessity of the DID Subject and whether the issuer supports it.   

##### CitizenShip
When the Issuer wants to provide the "CitizenShip" service to the DID Subject, it updates the "service" information in the DID Document of the DID Subject. The request message must include the issuer's digital signature.

* Request message
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1"
  ],
  "credentialSubject": {
    "id": "<Holder.DID>",
    "service": {
      "id": "<Issuer.DID>#svc-citizenship",
      "type": "CitizenShip",
      "serviceEndpoint": "https://citizen.example.co.kr"
    }
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2022-11-13T18:19:39Z",
    "verificationMethod": "<Issuer.DID>#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
  }
}
```

##### IotLoRa
When the Issuer wants to provide the "IotLoRa" service to the DID Subject, it updates the "service" information in the DID Document of the DID Subject. The request message must include the issuer's digital signature.

* Request message
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1"
  ],
  "credentialSubject": {
    "id": "<Holder.DID>",
    "service": {
      "id": "<Issuer.DID>#svc-iot-lora",
      "type": "IotLoRa",
      "serviceEndpoint": "https://lora.example.co.kr"
    }
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2022-11-13T18:19:39Z",
    "verificationMethod": "<Issuer.DID>#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
  }
}
```

##### IotWifi
When the Issuer wants to provide the "IotWifi" service to the DID Subject, it updates the "service" information in the DID Document of the DID Subject. The request message must include the issuer's digital signature.

* Request message
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1"
  ],
  "credentialSubject": {
    "id": "<Holder.DID>",
    "service": {
      "id": "<Issuer.DID>#svc-iot-wifi",
      "type": "IotWifi",
      "serviceEndpoint": "https://wifi.example.co.kr"
    }
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2022-11-13T18:19:39Z",
    "verificationMethod": "<Issuer.DID>#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
  }
}
```

#### Deactivate & Enactivate
DID Document can be activated or deactivated.
The request message must include the issuer's digital signature.

* Request message
```json
{
  "@context": [
    "https://www.w3.org/ns/did/v1"
  ],
  "credentialSubject": {
    "id": "<Holder.DID>",
    "deactivated": true
  },
  "proof": {
    "type": "Ed25519Signature2020",
    "created": "2022-11-13T18:19:39Z",
    "verificationMethod": "<Issuer.DID>#key-1",
    "proofPurpose": "assertionMethod",
    "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
  }
}
```

## References
- Decentralized Identifiers (DIDs) v1.0 https://www.w3.org/TR/2022/REC-did-core-20220719/
- DID Specification Registries https://www.w3.org/TR/2022/NOTE-did-spec-registries-20220809/
- JSON-LD 1.0 - A JSON-based Serialization for Linked Data https://www.w3.org/TR/json-ld
