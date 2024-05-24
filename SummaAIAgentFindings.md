# Summa AI Agent Findings

I fine-tuned a local LLM AI model and tinkered with Summa codebase for any interesting insights. Here are the selected findings from the AI agent on the Summa codebase:

## Vulnerability in InclusionVerifier.sol

- **Description of the Issue:**
The smart contract method `verifyProof` does not perform adequate validation on the lengths of the input arrays proofs, challenges, and values before proceeding with operations that assume their correctness. This lack of validation can lead to unexpected behavior or vulnerabilities.

- **File Path and Line Number(s):**
    File: `contracts/src/InclusionVerifier.sol`
    Lines: 23-27, 71-75, 77-81

- **Potential Impact:**
An attacker could exploit this vulnerability by passing in arrays of unexpected lengths, potentially causing the contract to behave unexpectedly, leading to denial of service or incorrect verification results. Specifically, incorrect lengths could manipulate the flow of the for loop (lines 71-75) and the logic that depends on these lengths.

- **Code Snippets:**
```solidity
// InclusionVerifier.sol
function verifyProof(
    address vk,
    bytes calldata proofs,
    uint256[] calldata challenges,
    uint256[] calldata values
) public view returns (bool) {
    ...
    let proof_length := calldataload(PROOF_LEN_CPTR)
    ...
    let challenges_length_pos := add(add(PROOF_LEN_CPTR, proof_length), 0x20)
    let challenges_length := calldataload(challenges_length_pos)
    ...
    let evaluation_values_length_pos := add(add(challenges_length_pos, mul(challenges_length, 0x20)), 0x20)
    let evaluation_values_length := calldataload(evaluation_values_length_pos)
    ...
}
```

- **Exploit PoC:**
An attacker could call `verifyProof` with proofs of length that does not match the expected format (e.g., not divisible by `0x80`), or with challenges and values arrays that do not align with the logic expected by the contract. This could lead to incorrect loop iterations or arithmetic operations based on unchecked lengths.

- **Recommendations:**
    - Input Validation: Before processing the inputs, explicitly check the lengths of the proofs, challenges, and values arrays to ensure they meet the expected criteria. For example, verify that proofs.length is divisible by `0x80`, and that the lengths of challenges and values are as expected.
    - Revert on Error: If the validation checks fail, revert the transaction to prevent any state changes or unintended behavior.
    - Use Solidity's Built-in Functions: Where possible, use Solidity's built-in functions for array length checks and other validations, as they are optimized for gas usage and security.

```solidity
require(proofs.length % 0x80 == 0, "Invalid proofs length");
require(challenges.length == expectedChallengesLength, "Invalid challenges length");
require(values.length == expectedValuesLength, "Invalid values length");
```

Implementing these recommendations will significantly reduce the risk associated with this vulnerability by ensuring that only inputs of expected lengths and formats are processed by the contract.

**2. SnarkVerifier.sol**

In the `SnarkVerifier.sol` contract, the `verifyProof` function processes input data from calldata without sufficient validation checks on the input sizes and values. This could lead to buffer overflow, underflow, or other unexpected behaviors.

```solidity
function verifyProof(
    address vk,
    bytes calldata proof,
    uint256[] calldata instances
) public view returns (bool) {
    ...
    success := and(success, eq(0x1500, calldataload(PROOF_LEN_CPTR)))
    let num_instances := mload(NUM_INSTANCES_MPTR)
    success := and(success, eq(num_instances, calldataload(NUM_INSTANCE_CPTR)))
    ...
}
```

The vulnerability here is the lack of validation for the actual content and length of the proof and instances arrays beyond just checking a hardcoded expected length (`0x1500` for proof). This does not ensure that the proof and instances contain valid or expected data, potentially allowing malformed or malicious data to be processed.
