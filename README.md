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
* DID Enroll Request : Holder to Issuer
```json
{
  "id" : "<Holder.DID>",
  "verificationMethod": [
    {
      "@context": [
        "https://www.w3.org/ns/did/v1"
      ],
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

### DID Infomation (Read)

Clients can get information of a specific DID by sending it to the Data-Alliance Verifiable Data Registry.   
After Requesting, a DID Document would return 
* DID Infomation Request : Client to Data-Alliance Verifiable Data Registry
<pre>
{
  "id" : "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87"
}
</pre>

### DID Update (Update)
DID Document는 업데이트가능하고 proof(전자서명)를 통해 신원인증 절차가 포함되어 있다
업데이트 항목은 public-key, deactivate 및 service 이다.
각 항목에 따라 필요 proof가 달라진다.

#### Public-Key Add & Update
Holder의 전자서명 필요, 전자서명은 SC에서 검증된다.   
추가하는 public-key는 "verificationMethod"에 정의하고 이미 존재하는 "id"이면 update이고 존재하지 않으면 add이다
이 "id"는 "proof-verificationMethod"와 달라야 한다.

<pre>
"credentialSubject": {
  "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87",  // ???
  "verificationMethod": [
    {
      "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#keys-2",
      "type": "EcdsaSecp256k1VerificationKey2019",
      "controller": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87",
      "publicKeyBase58": "zEY59y7px76e2yv5FMj9fYcjDsqk8yus6isWtkF69ZrHY"
    }
  ]
},
"proof": {
  "type": "Ed25519Signature2020",
  "created": "2021-11-13T18:19:39Z",
  "verificationMethod": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#key-1",
  "proofPurpose": "assertionMethod",
  "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
}
</pre>

#### Service Add

1. CitizenShip
  서비스도메인(Issuer)에 등록될때(VC가 발급될때) 추가된다.
  서비스도메인의 전자서명 필요, 전자서명은 SC에서 검증된다.

<pre>
"credentialSubject": {
  "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87",  // ???
  "service": {
    "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#svc-citizenship",
    "type": "CitizenShip",
    "serviceEndpoint": "https://citizen.example.co.kr"
  }
},
"proof": {
  "type": "Ed25519Signature2020",
  "created": "2021-11-13T18:19:39Z",
  "verificationMethod": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#key-1",
  "proofPurpose": "assertionMethod",
  "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
}
</pre>
  
2. IotLoRa
  서비스도메인(디바이스관리 도메인)에 등록될때 추가된다.
  서비스도메인의 전자서명 필요, 전자서명은 SC에서 검증된다.   

<pre>
"credentialSubject": {
  "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87",  // ???
  "service": {
    "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#svc-iot-lora",
    "type": "IotLoRa",
    "serviceEndpoint": "https://lora.example.co.kr"
  }
},
"proof": {
  "type": "Ed25519Signature2020",
  "created": "2021-11-13T18:19:39Z",
  "verificationMethod": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#key-1",
  "proofPurpose": "assertionMethod",
  "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
}
</pre>

3. IotWifi
  서비스도메인(디바이스관리 도메인)에 등록될때 추가된다.
  서비스도메인의 전자서명 필요, 전자서명은 SC에서 검증된다.

<pre>
"credentialSubject": {
  "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87",  // ???
  "service": {
    "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#svc-iot-wifi",
    "type": "IotWifi",
    "serviceEndpoint": "https://wifi.example.co.kr"
  }
},
"proof": {
  "type": "Ed25519Signature2020",
  "created": "2021-11-13T18:19:39Z",
  "verificationMethod": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#key-1",
  "proofPurpose": "assertionMethod",
  "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
}
</pre>

#### Deactivate & Enactivate
서비스도메인(Issuer)의 전자서명 필요, 전자서명은 SC에서 검증된다.

<pre>
"credentialSubject": {
  "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87",  // ???
  "deactivated": true      // or false
},
"proof": {
  "type": "Ed25519Signature2020",
  "created": "2021-11-13T18:19:39Z",
  "verificationMethod": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#key-1",
  "proofPurpose": "assertionMethod",
  "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
}
</pre>

### DID Rovoke (Delete)
서비스도메인(Issuer)의 전자서명 필요, 전자서명은 SC에서 검증된다.

<pre>
"credentialSubject": {
  "revocation": {
    "id": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87",  // ???
  }
},
"proof": {
  "type": "Ed25519Signature2020",
  "created": "2021-11-13T18:19:39Z",
  "verificationMethod": "did:dxd:0000dc13588923c083a70f3307de11d3f213657979c68c8b2f87#key-1",
  "proofPurpose": "assertionMethod",
  "proofValue": "z58DAdFfa9SkqZMVPxAQpic7ndSayn1PzZs6ZjWp1CktyGesjuTSwRdoWhAfGFCF5bppETSTojQCrfFPP2oumHKtz"
}
</pre>


## References
- Decentralized Identifiers (DIDs) v1.0 https://www.w3.org/TR/2022/REC-did-core-20220719/
- DID Specification Registries https://www.w3.org/TR/2022/NOTE-did-spec-registries-20220809/
- JSON-LD 1.0 - A JSON-based Serialization for Linked Data https://www.w3.org/TR/json-ld
