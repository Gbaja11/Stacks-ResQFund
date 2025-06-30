

# Stacks-ResQFund - Disaster-Relief Donation Smart Contract

A Clarity smart contract for managing **disaster-relief donations**, providing **admin-controlled recipient management**, secure **user donations**, recipient **withdrawals**, and contract-wide **governance controls**.

---

## 🧭 Overview

This smart contract is designed to facilitate transparent and secure donation handling for disaster relief efforts. It supports:

* Donations with configurable limits
* Admin-controlled recipient allocations
* Time-gated withdrawals by recipients
* Refunds (within cooldown)
* Emergency shutdowns
* On-chain event logging

---

## 🛠 Contract Features

### ✅ Donations

* Users can donate using `donate(amount)`.
* Enforces `min-donation` and `max-donation` thresholds.
* Tracks donations per user and total funds.

### 🎯 Recipient Management

* Admin can:

  * `add-recipient(principal, allocation)`
  * `update-recipient-allocation(principal, new-allocation)`
  * `remove-recipient(principal)`
  * `confirm-remove-recipient(principal, confirm)`
* Allocations are stored and updated securely.

### 💸 Withdrawals

* Recipients can `withdraw(amount)`:

  * Up to their allocated amount
  * Enforced 24-hour **cooldown** between withdrawals
* Transfers are made to a designated treasury address (`'ST000000000000000000002AMW42H`).

### 🔁 Refunds

* Donors can `request-refund(amount)` within 24 hours.
* Partial or full refunds are supported if eligible.

### ⚙ Admin Functions

* `set-admin(new-admin)`
* `set-donation-limits(min, max)`
* `set-paused(state)`
* `emergency-shutdown()` (sets `paused = true`)
* `log-audit(action, actor)` (basic placeholder)

### 🔎 Read-Only Functions

* `get-donation(user)`
* `get-total-funds()`
* `get-recipient-allocation(recipient)`
* `get-admin()`
* `is-paused()`
* `get-user-history(user)` — shows user donation and last withdrawal time.

---

## 📚 Data Structures

### Constants

| Name                  | Value       | Purpose                                     |
| --------------------- | ----------- | ------------------------------------------- |
| `withdrawal-cooldown` | 86400 (24h) | Time required between recipient withdrawals |
| `ERR_*`               | various     | Error codes for common failure cases        |

### Storage Variables

| Variable                        | Type        | Description                           |
| ------------------------------- | ----------- | ------------------------------------- |
| `admin`                         | `principal` | The contract admin                    |
| `paused`                        | `bool`      | Contract-wide pause flag              |
| `total-funds`                   | `uint`      | Sum of all unallocated/held donations |
| `min-donation` / `max-donation` | `uint`      | Bounds on allowed donations           |

### Maps

| Map               | Key         | Value  | Purpose                                |
| ----------------- | ----------- | ------ | -------------------------------------- |
| `donations`       | `principal` | `uint` | Total donations per user               |
| `recipients`      | `principal` | `uint` | Allocated amount for each recipient    |
| `last-withdrawal` | `principal` | `uint` | Timestamp of last recipient withdrawal |

---

## 🧪 Error Codes

| Code   | Name                      | Description                             |
| ------ | ------------------------- | --------------------------------------- |
| `u1`   | `ERR_UNAUTHORIZED`        | Unauthorized caller                     |
| `u2`   | `ERR_INVALID_AMOUNT`      | Invalid token amount                    |
| `u3`   | `ERR_INSUFFICIENT_FUNDS`  | Not enough balance                      |
| `u4`   | `ERR_RECIPIENT_NOT_FOUND` | Recipient not in system                 |
| `u5`   | `ERR_RECIPIENT_EXISTS`    | Recipient already exists                |
| `u6`   | `ERR_UNCHANGED_STATE`     | No change made                          |
| `u8`   | `ERR_NOT_CONFIRMED`       | Confirmation flag not true              |
| `u9`   | `ERR_UNCHANGED_STATE`     | Reused for pause toggle check           |
| `u100` | -                         | Invalid donation or contract paused     |
| `u101` | -                         | Unauthorized or invalid recipient input |
| `u102` | -                         | Invalid withdrawal or cooldown not met  |
| `u104` | -                         | Invalid min/max donation limits         |
| `u105` | -                         | Unauthorized shutdown                   |
| `u110` | -                         | Refund period expired or amount invalid |

---

## 🚀 Usage Flow

1. **Admin Setup**

   * Deploy contract (admin is set as deployer)
   * Configure donation limits
   * Add initial recipients

2. **Donor Interaction**

   * Users donate via `donate(amount)`
   * Donations are tracked per user

3. **Recipient Withdrawals**

   * Recipients withdraw allocated funds once per 24 hours

4. **Admin Governance**

   * Pause/unpause contract
   * Add/update/remove recipients
   * Transfer admin rights if needed

5. **Emergency Handling**

   * Emergency shutdown via `emergency-shutdown()`

---

## 📎 Notes

* Treasury address `'ST000000000000000000002AMW42H` is currently hardcoded for fund transfers — change as needed.
* Refund logic assumes user action within **24 hours** of donation.
* Event logging is primarily done via `print` calls for simplicity.

---
