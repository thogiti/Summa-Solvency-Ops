# Paper Review - Broken Proofs of Solvency in Blockchain Custodial Wallets and Exchanges

## Overview
- Comprehensive Analysis: The paper presents an in-depth analysis of proofs of solvency mechanisms in blockchain custodial wallets and exchanges.
- Identification of Vulnerabilities: It identifies several critical vulnerabilities in existing proofs of solvency (PoS) schemes that could undermine their effectiveness.
- Practical Implications: Highlights real-world implications of these vulnerabilities, including potential risks to users and the overall blockchain ecosystem.
- Mitigation Strategies: Proposes mitigation strategies to address identified vulnerabilities, enhancing the security and reliability of PoS.
- Future Directions: Suggests areas for future research to further strengthen proofs of solvency and protect against evolving threats.
- Contribution to Blockchain Security: It contributes significantly to the field of blockchain security by providing actionable insights for improving PoS mechanisms.

## Taxonomy and Notation
1. Proof of Liabilities (PoL) Protocol Participants:
	- `P`: The exchange or custodian acting as the prover.
	- `U`: The set of exchange customers or users acting as verifiers `(u1,u2,...,un)`.
	- `L`: The liabilities dataset to which `P` commits on a public bulletin board (such as a blockchain).
2. Properties Required for Auditability Proofs:
	- Security: Ensures `P` cannot hide or understate its liabilities.
	- Privacy: Guarantees any user `u_i` does not learn any extraneous information from the proof besides the inclusion of its account balance in `L`.
3. Types of Cryptocurrency Wallets/Exchanges:
	- Pure Non-Custodial (PNC): Full user control over on-chain assets.
	- Assisted Non-Custodial (ANC): Shared control over private keys for enhanced recoverability.
	- Omnibus Custodial (OC): Pooled funds on-chain with private ledger management.
	- Segregated Custodial (SC): Individual on-chain addresses controlled by the custodian.
4. Vulnerabilities in PoL Implementations:
	- Vulnerable summation tree.
	- Short hash collisions.
	- Shared user ID.
	- Inconsistent root commitment.
5. Mitigation Strategies:
	- Use of Pedersen commitments.
	- Implementation of sparse Merkle trees.
	- Ensuring unique user IDs.

## Important Keywords
• Blockchain Custodial Wallets: Digital wallets where the service provider has custody of the cryptographic keys.
• Solvency Proofs: Mechanisms to prove that an entity's assets exceed its liabilities.
• Light Clients: Blockchain clients that do not store the entire blockchain but can verify elements of it.
• Merkle Trees: A data structure used for efficiently summarizing and verifying the integrity of large data sets.
• Public Bulletin Board (PBB): A transparent, immutable ledger where commitments are published.
• Cryptographic Attacks: Techniques used to compromise security, such as hash-output truncation.
• Data Binding: The process of ensuring data integrity through cryptographic means.
• Hash-Truncation: Reducing the size of a hash output, which can lead to security vulnerabilities.
• Dispute Resolution: Mechanisms for resolving discrepancies in PoL proofs.
• Decentralization and (Pseudo)-Anonymity: Key characteristics of blockchain technology aiming for reduced central control and enhanced privacy.


