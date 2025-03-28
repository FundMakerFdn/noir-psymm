# noir-psymm

A project integrating pSymm contract with Noir ZK circuits for enhanced privacy.

## Circuits

- ATC (address to custody) - deposit funds to pSymm.
- CTC (custody to custody) - split/anonymize funds between pSymm custodies (without disclosing original commitment)
  - Current problem: mismatch of merkle tree root between Noir and Solidity, [fixed temporarily](https://github.com/FundMakerFdn/noir-psymm/blob/main/contracts/test/noirPsymm/index.js#L321) with `setRoot`.
- CTA (custody to address) - withdraw funds from pSymm. Currently not implemented.

## Installation

1. Install JavaScript dependencies:

   ```bash
   yarn install
   ```

2. Install Noir and the Barretenberg (BB) proving backend by following [official documentation](https://noir-lang.org/docs/getting_started/quick_start).

## Testing noir-psymm

Run the test suite with:

```bash
yarn hardhat test contracts/test/noirPsymm
```

## How to integrate a new Noir circuit

To create a new circuit (e.g., CTA based on ATC):

1. Copy an existing circuit as a template:

   ```bash
   cp -r noir/pSymmATC noir/pSymmCTA
   cd noir/pSymmCTA
   ```

2. Modify the circuit:

   - Update the circuit name in `Nargo.toml`
   - Make necessary modifications to `src/main.nr`

3. Compile the circuit:

   ```bash
   nargo compile
   ```

   This will generate `target/pSymmCTA.json`

4. Generate the verifier contract:

   ```bash
   bb write_vk -b target/pSymmCTA.json
   bb contract
   ```

   The contract will be generated at `target/contract.sol`

5. Copy the verifier contract to the contracts directory:
   ```bash
   cp target/contract.sol ../../contracts/src/noirPsymm/VerifierCTA.sol
   ```

## Current Issues

The merkle root generated by the Noir CTC circuit and JavaScript doesn't match the one generated in Solidity contract.
