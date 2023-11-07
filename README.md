# Overview

This review covers two key pieces:

- v2 of the delegate registry
- v2 of the delegate marketplace

The delegate registry is a standalone singleton database that aggregates onchain programmable access control. Users can link cold wallets to hot wallets, or specify individual token rights to delegate to other wallets. This separation of asset utility from the asset owner is a powerful primitive that enables the delegate marketplace. The delegate marketplace lets users wrap delegation rights into ERC721 tokens that can then be traded or transferred in the same way as any other NFT. The primary use-case here is utility rentals with zero counterparty risk, zero liquidation risk, zero overcollateralization requirements, and an order of magnitude greater capital efficiency.

v1 of the delegate registry has been live across many EVM chains since September 2022. While v1 is not in scope, auditors are encouraged to review its usage patterns at [https://etherscan.io/address/0x00000000000076a84fef008cdabe6409d2fe638b](https://etherscan.io/address/0x00000000000076a84fef008cdabe6409d2fe638b). A frontend interface across all deployed chains and testnets can be used at [https://delegate.xyz](https://delegate.xyz). The v2 registry is similar to v1, although with expanded support for fungible token amounts, cleaner enumeration methods, multicall transaction batching, gas efficiency improvements, and the introduction of subdelegation rights. Documentation for v1 can be found at [https://docs.delegate.xyz](https://docs.delegate.xyz).

The delegate marketplace consists of three core contracts: the DelegateToken, the PrincipalToken, and the CreateOfferer. Users will deposit a token, such as a bored ape NFT, into smart contract escrow using the DelegateToken.sol::create() function. They will receive back two ERC721s: a bored ape DelegateToken, and a bored ape PrincipalToken. The holder of the DelegateToken will receive delegate rights for the duration of the escrow. The holder of the PrincipalToken will have the right to redeem the bored ape from escrow at conclusion of the chosen timeframe. Users can choose to transfer or sell neither, one, or both of these. The CreateOfferer is a Seaport Contract Offerer that enables gasless listing of DelegateTokens which have not been created yet. If a buyer fulfills the gasless listing, then the desired token will be atomically escrowed and a DelegateToken created.

# Scope

| Contract | SLOC | Purpose | Libraries used |  
| ----------- | ----------- | ----------- | ----------- |
| DelegateRegistry.sol | 364 | v2 of the delegate registry | ??? |
| RegistryHashes.sol | 135 | Helper library for registry hash calculation | ??? |
| RegistryStorage.sol | 31 | Helper library for registry storage layout | ??? |
| RegistryOps.sol | 18 | Helper library for branchless boolean ops | ??? |
| DelegateToken.sol | 288 | Represent delegate rights as a transferrable ERC721 | ??? |
| PrincipalToken.sol | 43 | Represent the rights to claim the deposited token as a transferrable ERC721 | ??? |
| CreateOfferer.sol | 171 | Seaport Contract Offerer to enable gasless listings of DTs before they're created | ??? |
| CreateOffererLib.sol | 267 | Helper library for CreateOfferer | ??? |
| DelegateTokenLib.sol | 90 | Helper library for DelegateToken | ??? |
| DelegateTokenRegistryHelpers.sol | 219 | Helper library for Delegate Token registry hitchhiking | ??? |
| DelegateTokenStorageHelpers.sol | 125 | Helper library for Delegate Token registry hitchhiking | ??? |
| DelegateTokenTransferHelpers.sol | 73 | Helper library for Delegate Token ERC20/721/1155 transfers | ??? |

## Out of scope

The following contracts are out of scope:

- Any deployment or upgrade scripts are out of scope (but can be used to test the contracts in scope).
- Files in test/, contracts/test/ or src/test/ are out of scope (but can be used to test the contracts in scope).
- External libraries (@openzeppelin/*, @seaport/*)
- MarketMetadata.sol
- Singlesig.sol
- lib/delegate-registry/src/examples/*.sol

## Setup

Either clone with `--recurse`:

```bash
git clone --recurse 2023-09-delegate.git
```

Use one of the following if `--recurse` was forgotten:

```bash
git submodule update --init --recursive
forge install
```

Be sure to run `foundryup`:

```bash
foundryup
```

## Tests

```bash
# Run marketplace tests
forge test
# Run registry tests
cd lib/delegate-registry && forge test && cd ../..
# Run registry gas benchmarks
cd lib/delegate-registry && forge test --match-contract GasBenchmark --gas-report > gasbenchmark10mil && forge test --match-contract HashBenchmark --gas-report > hashbenchmark10mil && cd ../..
```

## Slither

To run Slither, use the following command:

```bash
mv test _test
slither .
mv _test test
```
