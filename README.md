# Data-Alliance Decentralized IDs Method Specification

## Introduction
DA소개 및 연락처
DID표준 버전
## Overview
The system aims to provide secure authentication and various services based on DID & Verifiable Credential Specifications published by the W3C.

### Method Name
## Specification
### Generating a Key-Pair
It is generated using the elliptic curve encryption algorithm(secp256k1) using Neal Koblitz elliptic curve. (See http://www.secg.org/sec2-v2.pdf)   
Key generation must be generated directly from the device of the DID Subject.

### Generating a unique idstring
A DA-DID(Data-Alliance DID) that uses this method **MUST** begin with the prefix "did:dxd", this prefix **MUST** be in lowercase.   
DA-DID format is descibed below.

![did:dxd_format](https://github.com/Data-Alliance/did-dxd/blob/master/did:dxd_format.png)

### DID Document Model
DA-DID (Data-Alliance DIDs) provides a DID Document that are mapped to the DID of registered Holder through Verifiable Data Registry.   
The DID Document does not contain any kind of "personal information" that can identify the holder, and the service provided through DA-DID
It contains only information for proof of identity and proof of authority.   
Under DA-DID, the transfer of personal information must be done only through an encrypted Verifiable Credential.   

![did:dxd_format](https://github.com/Data-Alliance/did-dxd/blob/master/did-document_format1.png)
![did:dxd_format](https://github.com/Data-Alliance/did-dxd/blob/master/did-document_format2.png)

## CRUD Operation
### DID Enroll (Create)
### DID Infomation (Read)
### DID Update (Update)
#### Public-Key Add & Update
#### Deactivate & Enactivate
### DID Rovoke (Delete)
## References
- Decentralized Identifiers (DIDs) v1.0 https://www.w3.org/TR/2022/REC-did-core-20220719/
- DID Specification Registries https://www.w3.org/TR/2022/NOTE-did-spec-registries-20220809/
- JSON-LD 1.0 - A JSON-based Serialization for Linked Data https://www.w3.org/TR/json-ld
