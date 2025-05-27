
# STX-LaunchPool - Pool Smart Contract
 A decentralized, trustless smart contract for collective venture capital investment and proposal-based fund distribution using community voting.

---

## 📜 Overview

The **STX-LaunchPool** enables groups of contributors to collectively fund a pool, submit investment proposals, vote on them, and execute proposals to fund startups. It provides a DAO-style mechanism for decentralized venture capital funding on the **Stacks blockchain**, written in **Clarity**.

This contract allows:

* Creation of funding pools
* Contribution to pools with STX
* Submission of investment proposals
* Voting on proposals based on contribution
* Execution of proposals after a voting period
* Fund disbursement to approved startups

---

## 🧩 Features

* **Pool Creation**: Anyone can create a new VC pool.
* **Minimum Contribution**: Ensures meaningful participation.
* **Voting Power**: Proportional to STX contributed.
* **Proposal Submission**: Enabled only when the pool reaches a funding threshold.
* **Voting Mechanism**: Allows one vote per contributor per proposal.
* **Timed Voting**: Ensures proposals are open for a fixed period.
* **Fund Execution**: Transfers funds to the startup if the majority votes pass.

---

## 🚀 Deployment

Ensure you are working with the [Stacks CLI](https://docs.stacks.co/docs/cli/overview/) or an environment that supports Clarity smart contracts.

Deploy using:

```bash
clarity-cli launch --contract crowd-vc-pool.clar
```

---

## 🛠 Contract Constants

| Constant             | Value        | Description                                        |
| -------------------- | ------------ | -------------------------------------------------- |
| `MIN_CONTRIBUTION`   | `u1000000`   | Minimum of 1 STX (in microSTX) to join a pool      |
| `VOTING_PERIOD`      | `u144`       | Voting period (\~24 hours on Stacks)               |
| `PROPOSAL_THRESHOLD` | `u100000000` | Pool must reach 100 STX before accepting proposals |

---

## 🗂 Data Structures

### Maps

* `pools`: Stores metadata for each pool
* `contributions`: Tracks each contributor’s STX in a pool
* `proposals`: Records submitted startup proposals
* `votes`: Keeps track of individual votes

### Data Variables

* `pool-counter`: Global counter to assign unique `pool-id`s
* `proposal-counter`: Global counter for `proposal-id`s

---

## 📘 Public Functions

### 1. `create-pool () → (ok uint)`

Creates a new pool. Returns the new pool ID.

---

### 2. `contribute (pool-id uint, amount uint) → (ok bool)`

Adds STX to a pool. Must be ≥ `MIN_CONTRIBUTION`.

* Fails if:

  * Pool doesn't exist or is inactive
  * Amount is below minimum

---

### 3. `submit-proposal (pool-id uint, startup principal, amount uint, description string-utf8[256]) → (ok uint)`

Submits a startup funding proposal.

* Requires:

  * Pool has ≥ `PROPOSAL_THRESHOLD`
  * Proposal amount ≤ total funds in pool

---

### 4. `vote (pool-id uint, proposal-id uint, vote-for bool) → (ok bool)`

Vote for or against a proposal.

* One vote per contributor per proposal
* Voting power = contribution amount

---

### 5. `execute-proposal (pool-id uint, proposal-id uint) → (ok bool)`

Executes the outcome after `VOTING_PERIOD` ends.

* Transfers STX to startup if approved
* Marks proposal as `executed` or `rejected`

---

## 🔍 Read-only Functions

* `get-pool (pool-id uint)`: Returns pool metadata
* `get-contribution (pool-id uint, contributor principal)`: Contributor's stake
* `get-proposal (pool-id uint, proposal-id uint)`: Proposal details
* `get-vote (pool-id uint, proposal-id uint, voter principal)`: Returns the vote

---

## ❗ Error Codes

| Error Code               | Description                        |
| ------------------------ | ---------------------------------- |
| `ERR_NOT_AUTHORIZED`     | Contributor not found              |
| `ERR_INSUFFICIENT_FUNDS` | Proposal amount > pool funds       |
| `ERR_POOL_NOT_FOUND`     | Invalid or inactive pool           |
| `ERR_INVALID_AMOUNT`     | Contribution below minimum         |
| `ERR_ALREADY_VOTED`      | Duplicate voting                   |
| `ERR_VOTING_CLOSED`      | Voting expired or already executed |
| `ERR_BELOW_THRESHOLD`    | Proposal threshold not met         |

---

## 🔒 Security Considerations

* Funds are held by the smart contract until executed.
* Voting period prevents immediate execution.
* Each user can only vote once per proposal.
* Startup address is validated at the time of execution.

---

## 🔄 Lifecycle Summary

```text
Create Pool → Contribute → Submit Proposal → Vote → Execute Proposal
```

---

