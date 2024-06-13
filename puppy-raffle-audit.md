
# Puppy Raffle Audit Report

Prepared by: Dhanraj Avhad
Lead Auditors: 



Assisting Auditors:

- None

# Table of contents
<details>

<summary>See table</summary>

- [Puppy Raffle Audit Report](#puppy-raffle-audit-report)
- [Table of contents](#table-of-contents)
- [About YOUR\_NAME\_HERE](#about-your_name_here)
- [Disclaimer](#disclaimer)
- [Risk Classification](#risk-classification)
- [Audit Details](#audit-details)
  - [Scope](#scope)
- [Protocol Summary](#protocol-summary)
  - [Roles](#roles)
- [Executive Summary](#executive-summary)
  - [Issues found](#issues-found)
- [Findings](#findings)
  - [High](#high)
    - [\[H-1\] Reentrancy attack in `PuppyRaffle::refund` allows entrant to drain contract balance](#h-1-reentrancy-attack-in-puppyrafflerefund-allows-entrant-to-drain-contract-balance)
    - [\[H-2\] Weak randomness in `PuppyRaffle::selectWinner` allows anyone to choose winner](#h-2-weak-randomness-in-puppyraffleselectwinner-allows-anyone-to-choose-winner)
    - [\[H-3\] Integer overflow of `PuppyRaffle::totalFees` loses fees](#h-3-integer-overflow-of-puppyraffletotalfees-loses-fees)
    - [\[H-4\] Malicious winner can forever halt the raffle](#h-4-malicious-winner-can-forever-halt-the-raffle)
  - [Medium](#medium)
    - [\[M-1\] Looping through players array to check for duplicates in `PuppyRaffle::enterRaffle` is a potential DoS vector, incrementing gas costs for future entrants](#m-1-looping-through-players-array-to-check-for-duplicates-in-puppyraffleenterraffle-is-a-potential-dos-vector-incrementing-gas-costs-for-future-entrants)
    - [\[M-2\] Balance check on `PuppyRaffle::withdrawFees` enables griefers to selfdestruct a contract to send ETH to the raffle, blocking withdrawals](#m-2-balance-check-on-puppyrafflewithdrawfees-enables-griefers-to-selfdestruct-a-contract-to-send-eth-to-the-raffle-blocking-withdrawals)
    - [\[M-3\] Unsafe cast of `PuppyRaffle::fee` loses fees](#m-3-unsafe-cast-of-puppyrafflefee-loses-fees)
    - [\[M-4\] Smart Contract wallet raffle winners without a `receive` or a `fallback` will block the start of a new contest](#m-4-smart-contract-wallet-raffle-winners-without-a-receive-or-a-fallback-will-block-the-start-of-a-new-contest)
  - [Informational / Non-Critical](#informational--non-critical)
    - [\[I-1\] Floating pragmas](#i-1-floating-pragmas)
    - [\[I-2\] Magic Numbers](#i-2-magic-numbers)
    - [\[I-3\] Test Coverage](#i-3-test-coverage)
    - [\[I-4\] Zero address validation](#i-4-zero-address-validation)
    - [\[I-5\] \_isActivePlayer is never used and should be removed](#i-5-_isactiveplayer-is-never-used-and-should-be-removed)
    - [\[I-6\] Unchanged variables should be constant or immutable](#i-6-unchanged-variables-should-be-constant-or-immutable)
    - [\[I-7\] Potentially erroneous active player index](#i-7-potentially-erroneous-active-player-index)
    - [\[I-8\] Zero address may be erroneously considered an active player](#i-8-zero-address-may-be-erroneously-considered-an-active-player)
  - [Gas (Optional)](#gas-optional)
</details>
</br>

# About YOUR_NAME_HERE

<!-- Tell people about you! -->

# Disclaimer

The YOUR_NAME_HERE team makes all effort to find as many vulnerabilities in the code in the given time period, but holds no responsibilities for the the findings provided in this document. A security audit by the team is not an endorsement of the underlying business or product. The audit was time-boxed and the review of the code was solely on the security aspects of the solidity implementation of the contracts.

# Risk Classification

|            |        | Impact |        |     |
| ---------- | ------ | ------ | ------ | --- |
|            |        | High   | Medium | Low |
|            | High   | H      | H/M    | M   |
| Likelihood | Medium | H/M    | M      | M/L |
|            | Low    | M      | M/L    | L   |

# Audit Details

**The findings described in this document correspond the following commit hash:**
```
22bbbb2c47f3f2b78c1b134590baf41383fd354f
```

## Scope 

```
./src/
-- PuppyRaffle.sol
```

# Protocol Summary 

Puppy Rafle is a protocol dedicated to raffling off puppy NFTs with variying rarities. A portion of entrance fees go to the winner, and a fee is taken by another address decided by the protocol owner. 

## Roles

- Owner: The only one who can change the `feeAddress`, denominated by the `_owner` variable.
- Fee User: The user who takes a cut of raffle entrance fees. Denominated by the `feeAddress` variable.
- Raffle Entrant: Anyone who enters the raffle. Denominated by being in the `players` array.

# Executive Summary

## Issues found

| Severity | Number of issues found |
| -------- | ---------------------- |
| High     | 4                      |
| Medium   | 3                      |
| Low      | 0                      |
| Info     | 8                      |
| Total    | 0                      |

# Findings
