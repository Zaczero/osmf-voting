# Specification for OSMF Electronic Voting System 1.0

## Abstract

Introducing the OpenStreetMap Foundation (OSMF) electronic voting system, a new approach to board elections. This system prioritizes privacy and cryptographic security, incorporating blockchain technology for a transparent, decentralized ballot collection. It employs blinded signatures to confirm voting rights anonymously and uses asymmetric cryptography to secure voting confidentiality until the deadline.

## System Overview

The voting system is a multi-stage process, designed to preserve voter anonymity and ensure security throughout registration, funding, and voting stages.

### Stage 1: Member Registration and Token Generation

1. **Application Release**: The Foundation releases a self-contained .html file with complete logic (HTML, JS, CSS) necessary for the voting process. This file is immutable and publicly auditable.

2. **Member Participation**: Members willing to vote download the .html application and install a cryptocurrency wallet as a browser extension. The Foundation sponsors the voting, so no wallet funding is needed by users.

3. **OAuth Authorization**: The application performs OAuth authorization with the Foundation server, obtaining an access token for further interactions.

4. **Encryption-Token Generation**: The application generates an encryption-token. Members sign this token, creating a private, deterministic encryption-key for encrypting sensitive local content.

5. **Secret Token Generation**: Two tokens are generated - a secret voting-token and funding-token, prefixed with "voting:" and "funding:" respectively.

6. **Blinding Process**: Both tokens are [blinded](https://en.wikipedia.org/wiki/Blinding_(cryptography)), creating blind-voting-token and blind-funding-token. These, along with the user's access token, are sent to the Foundation server for the signing process.

7. **Server Validation and Signing**: The Foundation server validates the voting rights of the member and signs the blinded tokens, ensuring this is the member's first signing request.

8. **Receiving Signed Tokens**: The application receives signatures for both blinded tokens, which are then unblinded to obtain valid signatures for the voting and funding tokens, voting-token-signature and funding-token-signature.

9. **Storing Signed Tokens**: The application stores the signed tokens in the encrypted data-file, which is saved locally on the member's computer.

#### Composition of the Data File

The data-file is a crucial component of the voting system. It contains sensitive information necessary for participating in the voting process. The contents of the data file include:

- **Encryption-Token**: A unique token used to derive the encryption-key.

- **Encrypted Payload**: A securely encrypted segment containing critical elements for the voting process. It is encrypted using the encryption-key derived from the encryption-token.

Upon launching the application, users upload their data-file. The application reads the encryption-token, requests the user's signature to derive the encryption-key, and decrypts the encrypted payload.

#### Encrypted Payload Contents

The encrypted payload within the data file includes:

- **Voting-Token**: A unique token used to cast the vote.

- **Voting-Token-Signature**: The signature provided by the Foundation, verifying the authenticity and eligibility of the voting-token.

- **Funding-Token**: A token used to receive funds from the Foundation, if the member opts to use Foundation-sponsored funding.

- **Funding-Token-Signature**: The Foundation's signature on the funding-token, validating its authenticity.

### Stage 1.5: Transparency Report

1. **Transparency Report**: Before voting, the Foundation releases a document listing members participating in the vote, without personal information. This allows members to additionally verify their vote registration and identify discrepancies.

### Stage 2: Funding Stage

To prevent de-anonymization by the Foundation, members are advised to use proxy/VPN/Tor when retrieving funds. This guarantees that the Foundation cannot link the member's IP address to the voting process.

1. **Funding Request**: Members have the option to exchange their funding-token for funds from the Foundation. This step is to cover transaction costs associated with the voting process on the blockchain.

2. **Funding Token Validation**: The Foundation validates the funding-token as well as the funding-token-signature and sends the funds to the member's wallet. Each token can only be used once.

### Stage 3: Voting Stage

With the completion of the funding stage, members are now equipped with their voting-token, signature, and the necessary funds to cover transaction costs on the blockchain.

1. **Presentation of Candidates**: The application displays a list of candidates for the election, randomly sorted to ensure unbiased selection.

2. **Broadcasting the Vote**: Members select their preferred candidates in order and then broadcast their vote on the blockchain. The broadcast transaction contains an encrypted payload, which is encrypted with the voting-public-key and includes the voting-token, its signature, and the member's vote.

### Stage 4: Vote Counting

1. **Publishing the Voting-Private-Key**: Once the voting deadline is reached, the Foundation publishes the voting-private-key. This key is used to decrypt all votes cast during the voting period.

2. **Blockchain Scanning**: The application scans the blockchain for all transactions related to the voting procedure, considering only those made within the voting period.

3. **Vote Decryption and Validation**: Using the voting-private-key, the application decrypts the votes. It validates each vote by checking the "voting:" prefix in the voting-token, the authenticity of the voting-token-signature, and ensuring no double voting has occurred. Only the earliest occurrence of each valid voting-token is considered.

4. **Member List Comparison**: The total count of valid votes is compared with the size of the member list in the Foundation's pre-voting transparency report. If the number of votes exceeds the number of listed members, the voting process is deemed invalid.

5. **Independent Result Calculation**: The application independently calculates the voting results based on valid votes. These results are displayed to the user, along with a simple audit log for transparency and verification purposes.

## Security Guarantees

- **Immutable Application**: The application is immutable and publicly auditable. There is no self-update mechanism and no external dependencies.

- **Eligibility and Anonymity**: Non-eligible members cannot participate, and eligible members remain anonymous.

- **One Vote Per Member**: Each member can vote only once.

- **Blinded Signature Security**: The Foundation cannot correlate voter identities with their votes.

- **Ballot Confidentiality**: Ballots remain hidden to the public until the deadline.

- **Independent Verification**: Results are calculated from on-chain data and can be independently verified.

## Limitations

- **Risk of Artificial Members**: A malicious Foundation could create artificial members to influence the voting process. This risk is partially mitigated by the Foundation's transparency report, which allows members to verify their participation and identify discrepancies.

- **Technical Complexity for Users**: Setting up a cryptocurrency wallet and understanding the voting process may be challenging for some members. To ease out the process, the application provides a step-by-step guide and a video tutorial.

- **Potential Deanonymization during Funding Stage**: Members not using proxy/VPN/Tor during the funding stage may be de-anonymized by a malicious Foundation through their IP address. This risk is communicated to members, with advice to use privacy-enhancing tools.

- **Blockchain Provider Trust**: Relies on the blockchain provider's integrity. Malicious actions by the provider are detectable through data source comparisons.

- **Foundation Access to Ballots**: The Foundation manages the voting-private-key and can decrypt votes before the deadline.

## OpaVote vs OSMF-Voting

| | OpaVote | OSMF-Voting |
| --- | --- | --- |
| Open Source | ❌ proprietary | ✅ free and open source |
| Privacy | ❌ "trust us", google-analytics tracking on the voting page, external dependencies | ✅ no external dependencies, no tracking |
| Security Guarantees | ❌ "trust us" | ✅ verifiable, mathematically proven |
| "Artificial Members" Attack | ❌ possible | 🟨 partially mitigated |
| Voter Eligibility | ❓ trust OpaVote and OSMF | 🟨 trust OSMF |
| Voter Anonymity | ❓ trust OpaVote | ✅ cryptographically secured |
| One Vote Per Member | ❓ trust OpaVote | ✅ cryptographically secured |
| Ballots Confidentiality | ❓ trust OpaVote | 🟨 trust OSMF |
| Results Verification | ❓ trust OpaVote | ✅ independently verifiable |
| Code Audits | ❌ no independent audits | ✅ public audits |

## License

[osmf-voting](https://github.com/Zaczero/osmf-voting) by [Kamil Monicz (Zaczero)](https://monicz.dev) is licensed under [CC BY-NC-SA 4.0](http://creativecommons.org/licenses/by-nc-sa/4.0/)

![CC](https://mirrors.creativecommons.org/presskit/icons/cc.svg) ![BY](https://mirrors.creativecommons.org/presskit/icons/by.svg) ![NC](https://mirrors.creativecommons.org/presskit/icons/nc.svg) ![SA](https://mirrors.creativecommons.org/presskit/icons/sa.svg)
