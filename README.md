# Data-Alliance Decentralized IDs Method Specification

## Introduction
DA소개 및 연락처
DID표준 버전
## Overview
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
