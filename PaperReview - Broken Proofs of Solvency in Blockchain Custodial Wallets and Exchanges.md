# Paper Review - Broken Proofs of Solvency in Blockchain Custodial Wallets and Exchanges

## Citation
@misc{cryptoeprint:2022/043,
      author = {Konstantinos Chalkias and Panagiotis Chatzigiannis and Yan Ji},
      title = {Broken Proofs of Solvency in Blockchain Custodial Wallets and Exchanges},
      howpublished = {Cryptology ePrint Archive, Paper 2022/043},
      year = {2022},
      note = {\url{https://eprint.iacr.org/2022/043}},
      url = {https://eprint.iacr.org/2022/043}
}


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
- Blockchain Custodial Wallets: Digital wallets where the service provider has custody of the cryptographic keys.
- Solvency Proofs: Mechanisms to prove that an entity's assets exceed its liabilities.
- Light Clients: Blockchain clients that do not store the entire blockchain but can verify elements of it.
- Merkle Trees: A data structure used for efficiently summarizing and verifying the integrity of large data sets.
- Public Bulletin Board (PBB): A transparent, immutable ledger where commitments are published.
- Cryptographic Attacks: Techniques used to compromise security, such as hash-output truncation.
- Data Binding: The process of ensuring data integrity through cryptographic means.
- Hash-Truncation: Reducing the size of a hash output, which can lead to security vulnerabilities.
- Dispute Resolution: Mechanisms for resolving discrepancies in PoL proofs.
- Decentralization and (Pseudo)-Anonymity: Key characteristics of blockchain technology aiming for reduced central control and enhanced privacy.

## Main Contributions
- The paper synthesizes insights from recent academic research, discussions with blockchain researchers, and interactions with auditing firms and blockchain organizations.
- It identifies multiple vulnerabilities in existing PoL implementations, revealing a range of security issues that could potentially compromise the trust users place in these systems.
- Offers a critical discussion aimed at addressing these vulnerabilities, with the intent of bridging the theoretical and practical aspects of PoL implementations.

## Existing PoL Schemes
### Provisions
- Commit each user's balance on a PBB using homomorphic Pedersen commitments.
- Prove balances are positive in a zero-knowledge manner to conceal values.
- Challenges: Linear commitment size and public number of users; requires dummy accounts for privacy.

### Maxwell-Todd
- Utilize a summation Merkle tree, adding a value field  `v` to each node for summing child node values.
- Publish Merkle root, with root value representing total liabilities. Users can query and verify their inclusion.
- Vulnerability: Allows underreporting of liabilities by manipulating node values.

### Maxwell+
- Modification: Include both values and hashes of child nodes in each node field `h=H( vl ∣∣ vr ∣∣ hl ∣∣ hr)`.
- Outcome: Binds node values in the parent, preventing previous manipulation but not addressing privacy concerns.

### Maxwell++
- Extension: Conceals population and individual liabilities by breaking and shuffling values, without hiding total liabilities.

### Camacho
- Approach: Uses Pedersen commitments instead of plain values in the Maxwell-Todd scheme and incorporates zero-knowledge range proofs.
- Privacy: Provides privacy and prevents manipulation but leaks the number of users and shares Maxwell-Todd's flaw.

### DAPOL and DAPOL+
- Improvement: Incorporates Maxwell+'s fix and uses sparse Merkle trees (SMT) to hide the number of users more efficiently.


## Vulnerabilities Overview
- Gap Between Theory and Practice: Despite extensive theoretical research on PoL, a significant gap remains between academic works and real-world implementations, leading to vulnerabilities.
- Limited Adoption: Among hundreds of blockchain exchanges, only a few support PoL, with some relying on trusted auditors, which raises concerns about potential collusion and the reliability of such audits.
- Exploitable Attacks: Real-world implementations of PoL are vulnerable to various attacks, potentially allowing exchanges to understate liabilities without malicious intent but due to lack of proper auditing or cryptographic expertise.
- Problematic Implementations Highlighted:
  - Vulnerable Summation Tree: Utilized by entities like BHEX and audited by Deloitte, where liabilities could be understated through manipulation of the summation process.
  - Short Hash Collisions: Observed in BHEX’s implementation, where hash truncation increases the possibility of collisions, leading to under-reported liabilities.
  - Shared User ID: Instances in Coinfloor, Kraken, and BitMEX where the uniqueness of user IDs is not guaranteed, potentially leading to liability hiding.
  - Inconsistent Root Commitment: Seen in Coinfloor and BitMEX, where multiple root commitments could lead to different users receiving proofs bound to different commitments.

## Vulnerable Summation Tree

### Exploitation Process (Fig 1 & 2)
1. Initial Setup: The summation tree is constructed with each node's hash field defined as h(vl+vr ∣∣ h(l) ∣∣ h(r)), where vl and vr are the values of the left and right child nodes, respectively, and l and r denote the child nodes themselves.
2. Manipulation: The exchange manipulates the computation for each parent node in the tree to only reflect the maximum value of the children's summed values (vl + vr). This is done by computing each parent as h(max(vl + vr) ∣∣ h(l) ∣∣ h(r)), effectively creating a different "version" of the liabilities tree without detection by clients.
3. Understating Liabilities: Through this manipulation, the exchange can understate its total liabilities. For example, if two children have values vl = 5 and vr = 0, only the higher value (in this case, vl) would contribute to the parent's value, ignoring the other liabilities.
4. Client Deception: A client receives a proof with a valid Merkle path that appears correct but does not accurately reflect all liabilities. This allows the exchange to report less than its actual total liabilities, potentially to the extent of only reporting the highest individual user balance as its total liabilities.

### Mitigation Strategy
1. Tree Redefinition: The mitigation strategy involves redefining the hash field of each node in the tree to include both the left and right child values separately, alongside their hashes. Specifically, each node is defined as h(vl ∣∣ vr ∣∣ h(l) ∣∣ h(r)).
2. Elimination of Manipulation: By including both vl and vr separately in the node's hash, the exchange cannot manipulate the tree to understate liabilities without detection. This construction ensures that the summation of liabilities is accurately represented at each node, preventing the creation of alternate "versions" of the liabilities tree.

## Short Hash Collisions
### Exploitation of Short Hash Collisions
1. Initial Setup: The PoL implementation uses a 64-bit truncated SHA256 hash for all nodes in the tree. This level of truncation increases the probability of hash collisions due to the reduced hash space.
2. Finding Collisions: An adversary or the exchange itself seeks a hash collision for two different user balances. This means finding two different inputs (i.e., user account balances or transaction details) that produce the same 64-bit truncated hash output.
3. Collision Assignment: Once a collision is found, the exchange can assign both users to the same leaf node in the Merkle tree. This is because both inputs, despite being different, have the same hash representation in the tree.
4. Under-Reporting Liabilities: With both users assigned to the same leaf node, the exchange can return the same Merkle path for both users. This manipulation allows the exchange to under-report its total liabilities without detection by users, as each user verifies their inclusion in the liabilities tree without realizing the overlap.

### Mitigation Strategy
1. Avoid Hash Truncation: The primary recommendation is to avoid truncating hashes within the PoL scheme. Full-length SHA256 hashes should be used for all nodes in the tree to drastically reduce the probability of collisions.
2. Enhanced Security Measures: Implement additional security checks and balances within the PoL scheme to detect and prevent hash collisions. This could include the use of more secure hash functions or additional layers of cryptographic verification.
3. Regular Audits and Verifications: Regular and thorough audits of the PoL implementation, including stress testing for hash collisions, can help identify potential vulnerabilities before they can be exploited.

## Shared User ID Problem
### Exploitation Process
1. Proof of Liabilities (PoL) Schemes: These schemes are designed to verify that an exchange or custodian (P) has sufficient assets to cover its liabilities towards users (U).
2. User ID Uniqueness Flaw: In the flawed implementations, PoL schemes do not ensure the uniqueness of user IDs. Exchanges like Coinfloor, Kraken, BitMEX, and others have been observed using non-unique user IDs.
3. Exploitation of Non-Uniqueness: A malicious prover (P) can exploit this flaw by assigning the same user ID to different users, especially when these users have identical balance amounts. This results in mapping multiple users to the same leaf node in the Merkle tree.
4. Hiding Liabilities: By reusing the same user ID for different users, the prover can effectively hide liabilities. This is because the proofs of inclusion provided to users do not uniquely bind to their accounts, allowing the prover to claim less total liabilities without detection.

### Mitigation Strategy
1. Ensuring User ID Uniqueness: The primary mitigation strategy involves ensuring the uniqueness of user IDs. This can be achieved by incorporating unique user credentials (e.g., unique usernames, email addresses) in the process of deriving these IDs.
2. Adherence to Privacy Regulations: To comply with regulations like the EU's GDPR, exchanges can request users to provide a long, unique ID that does not involve hashing private information such as email addresses.
3. Implementation of Advanced Schemes: The paper suggests adopting advanced constructions like Maxwell++ or DAPOL+, which involve binding unique user credentials with user IDs and all corresponding leaf nodes in the Merkle tree. This ensures that each user's balance is uniquely represented and verified without compromising privacy.
4. Practical Considerations: Exchanges should consider practical aspects such as the potential need for user education on the importance of maintaining unique identifiers and the technical adjustments required to existing systems to support unique IDs effectively.

## Multiple or Inconsistent Root Commitments
### Exploitation Process
1. Commitment Publishing: In a secure PoL scheme, the prover (P) is required to publish the root commitment of the liabilities tree on a public bulletin board (PBB), like a blockchain, to ensure all users have a consistent view of the public commitment.
2. Vulnerability Exploitation:
	- A malicious prover can exploit this by providing different commitments to different users.
	- These commitments, while individually valid, are not bound to the same root commitment.
	- This allows the prover to effectively omit certain liabilities from each commitment without detection, as users verifying their proofs against these different commitments would still find them valid.
3. Example Scenario:
	- Coinfloor and BitMEX were mentioned as examples where commitments were not consistently published on a PBB.
	- Coinfloor publishes a hash of the report file on the blockchain but then publishes the transaction ID on its website, which allows for potential manipulation when serving this information from its own controlled platform.

### Mitigation Strategy
1. Commitment on PBB:
	- The primary mitigation strategy involves consistently publishing the root commitment on a reliable and immutable PBB, such as a blockchain.
	- This ensures the non-equivocation of the commitment, as the blockchain's immutability property prevents altering the published commitment.
2. Consistent Commitment Verification:
	- Users should verify their proofs against the commitment published on the PBB.
	- They should also ensure that no more than one commitment for the same solvency round is published, preventing the prover from using multiple commitments to hide liabilities.
3. Enhanced Transparency and Security:
	- As an additional layer of security, commitments could be digitally signed by a trusted auditor.
	- This not only reinforces the trustworthiness of the commitment but also provides an extra verification layer for users.

## Privacy Concerns in PoL
### Exploitation of Privacy Concerns
1. Leakage of Individual Liabilities: PoL implementations, such as those by BHEX, Deloitte’s audits, and Coinfloor, may inadvertently reveal individual user liabilities. When users receive PoL proofs, these proofs could contain the balances of one or multiple other users, compromising the privacy of individual liabilities.
2. Exposure of Total User Count: Additionally, these schemes may leak the total number of users. The structure and size of the Merkle tree used in PoL proofs can give away information about the total number of users, further compromising privacy.

## Mitigation Strategies
1. Use of Pedersen Commitments: To hide individual liabilities, the paper recommends employing Pedersen commitments, as done in the DAPOL+ scheme. Pedersen commitments allow the hiding of the actual values while still enabling the aggregation of liabilities to prove the total without revealing individual amounts.
2. Sparse Merkle Trees (SMT): To mitigate the leakage of the total number of users, using sparse Merkle trees is suggested. Sparse Merkle trees can efficiently represent a set with a potentially large number of elements (users), where most of the tree is unoccupied, thus obscuring the exact count of users.
3. Efficiency Considerations: While splitting each account into multiple entries (as seen in BitMEX) can also address privacy concerns, it significantly impacts the efficiency of the PoL scheme due to the increased number of entries.
4. Recommendations for Exchanges:
	- Adopt advanced cryptographic techniques like Pedersen commitments and sparse Merkle trees to enhance privacy without sacrificing the integrity of PoL proofs.
	- Carefully design PoL implementations to ensure that they do not inadvertently reveal sensitive information about users or the total liabilities.

## Dispute Resolution
1. Open Research Problem: Dispute resolution within PoL schemes remains an open research problem. There is currently no established mechanism or protocol to resolve disputes between an exchange and a user in the presence of a third-party judge.
2. Scenario of Dispute: A dispute may arise when a user fails to verify a liability proof and challenges the exchange's claim that the user's account balance is indeed included in the liabilities proof.
3. Lack of Resolution Mechanism: The absence of a resolution mechanism means there's no structured process for a user to challenge the proof provided by the exchange, leading to potential trust issues.

## Private Verification Pattern
1. Verification Behavior: Not all users query for PoL proofs and perform verification, leading to a pattern where some users may never verify their inclusion in the liabilities proof.
2. Exploitation by Malicious Provers: If a malicious prover (exchange) identifies users who do not perform verifications, it can safely omit their balances in the next solvency round without being detected.
3. Hiding Verification Patterns: It would be beneficial to conceal users’ verification patterns from the prover to prevent targeted omissions of balances, ensuring all users' balances are consistently included in PoL proofs.
4. Outsourcing to Trusted Auditors: One mitigation strategy is outsourcing proof generation to a trusted auditor, as seen in Kraken's approach. This can help mitigate issues related to private verification patterns but introduces privacy concerns regarding the auditor's knowledge of individual inclusions.
5. Challenges and Potential Solutions: Efficiently hiding verification patterns without relying on a trusted third party remains a challenge. The paper mentions private information retrieval techniques as a proposed solution but acknowledges the complexity and open research questions in this area.




