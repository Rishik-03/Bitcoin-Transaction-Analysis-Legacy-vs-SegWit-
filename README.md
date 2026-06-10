# Bitcoin Transaction Analysis (Legacy vs SegWit)

## Team Name

**Strange Coins**

## Team Members

| Name          | Roll Number |
| ------------- | ----------- |
| Charan Kumar  | 240001057   |
| Shiv Pratap   | 240001069   |
| Rishik        | 240003027   |
| Aravind Nayak | 240003082   |

---

# Project Overview

This project analyzes Bitcoin transactions created in **Bitcoin Core Regtest Mode** and compares:

* Legacy P2PKH Transactions
* SegWit P2SH-P2WPKH Transactions (Nested SegWit)

The program parses transaction JSON outputs using **C** and the **cJSON library** and compares:

* Transaction Size
* Virtual Size (vsize)
* Weight
* Script Structure

The objective is to understand why **SegWit transactions are more efficient than Legacy transactions**.

---

# Repository Structure

```text
team_strange_coins/
│
├── bitcoin_assignment_.c
├── cJSON.c
├── cJSON.h
│
├── legacy_A_B.json
├── legacy_B_C.json
├── segwit_A_B.json
├── segwit_B_C.json
│
├── Report.pdf
└── README.md
```

---

# Requirements

The following software is required:

* Bitcoin Core
* GCC Compiler
* cJSON Library

Bitcoin Core is used in **Regtest Mode** to generate and analyze test transactions.

---

# Transaction Workflow

## Legacy Transactions

```text
Miner → Address A → Address B → Address C
```

## SegWit Transactions

```text
Miner → Address A' → Address B' → Address C'
```

---

# Part 1 — Legacy P2PKH Transactions

The first part creates and broadcasts a chain of **Legacy Pay-to-Public-Key-Hash (P2PKH)** transactions.

### Process

1. Create a wallet
2. Generate addresses
3. Mine blocks to obtain spendable coins
4. Fund Address A
5. Create transaction A → B
6. Create transaction B → C
7. Decode raw transactions
8. Analyze transaction scripts

The locking script follows the standard P2PKH structure, requiring a valid signature and public key to spend funds.

---

## Commands Used

### Create Wallet

```bash
bitcoin-cli -regtest createwallet LabWallet
```

> Note: The wallet name "LabWallet" was used instead of "LegacyWallet".

### Generate Address

```bash
bitcoin-cli -regtest -rpcwallet=LabWallet getnewaddress
```

### Generate Blocks

```bash
bitcoin-cli -regtest generatetoaddress 101 ADDRESS
```

### Send Coins

```bash
bitcoin-cli -regtest -rpcwallet=LabWallet sendtoaddress ADDRESS 10
```

### Mine a Confirmation Block

```bash
bitcoin-cli -regtest generatetoaddress 1 ADDRESS
```

### Decode Transaction

```bash
bitcoin-cli -regtest decoderawtransaction HEX
```

### Save Outputs

```text
legacy_A_B.json
legacy_B_C.json
```

---

# Part 2 — SegWit P2SH-P2WPKH Transactions

The second part implements **Nested SegWit** transactions using P2SH-wrapped P2WPKH addresses.

### Process

1. Create SegWit wallet
2. Generate SegWit addresses
3. Fund Address A'
4. Create transaction A' → B'
5. Create transaction B' → C'
6. Decode transactions
7. Compare with Legacy transactions

Unlike Legacy transactions, signatures are stored inside the witness structure instead of the scriptSig field.

This reduces transaction weight and virtual size.

---

## Commands Used

### Create SegWit Wallet

```bash
bitcoin-cli -regtest createwallet SegWitWallet
```

### Generate SegWit Address

```bash
bitcoin-cli -regtest -rpcwallet=SegWitWallet getnewaddress "" p2sh-segwit
```

### Generate Blocks

```bash
bitcoin-cli -regtest generatetoaddress 101 ADDRESS
```

### Send Coins

```bash
bitcoin-cli -regtest -rpcwallet=SegWitWallet sendtoaddress ADDRESS 10
```

### Mine a Confirmation Block

```bash
bitcoin-cli -regtest generatetoaddress 1 ADDRESS
```

### Decode Transaction

```bash
bitcoin-cli -regtest decoderawtransaction HEX
```

### Save Outputs

```text
segwit_A_B.json
segwit_B_C.json
```

---

# Compilation

Navigate to the project directory:

```bash
cd team_strange_coins
```

Compile the program:

```bash
gcc bitcoin_assignment_.c cJSON.c -o bitcoin_assignment
```

---

# Running the Program

Linux/macOS:

```bash
./bitcoin_assignment
```

Windows:

```bash
bitcoin_assignment.exe
```

---

# Example Output

```text
==================================================
LEGACY P2PKH TRANSACTION A -> B

size   : 191
vsize  : 191
weight : 764

scriptPubKey.type : pubkeyhash

==================================================
SEGWIT P2SH-P2WPKH TRANSACTION A' -> B'

size   : 247
vsize  : 166
weight : 661

scriptPubKey.type : scripthash

==================================================
LEGACY VS SEGWIT COMPARISON

Transaction      Size   Vsize   Weight   Type
------------------------------------------------
Legacy A->B      191    191     764      pubkeyhash
Legacy B->C      191    191     764      pubkeyhash
SegWit A'->B'    247    166     661      scripthash
SegWit B'->C'    247    166     661      scripthash
```

---

# Analysis

## Legacy P2PKH Transactions

Characteristics:

* Signature stored in `scriptSig`
* Public key stored in `scriptSig`
* Larger effective transaction size
* Higher transaction fees

### Script Structure

```text
scriptSig → Signature + Public Key
```

---

## SegWit P2SH-P2WPKH Transactions

Characteristics:

* Signature stored in witness data
* Reduced virtual size
* Lower transaction fees
* Eliminates transaction malleability

### Script Structure

```text
scriptSig    → Redeem Script
txinwitness  → Signature + Public Key
```

---

# Legacy vs SegWit Comparison

| Feature                 | Legacy P2PKH     | SegWit P2SH-P2WPKH |
| ----------------------- | ---------------- | ------------------ |
| Signature Location      | scriptSig        | Witness Data       |
| Transaction Size        | Smaller Raw Size | Larger Raw Size    |
| Virtual Size            | Larger           | Smaller            |
| Weight                  | Higher           | Lower              |
| Transaction Fee         | Higher           | Lower              |
| Malleability Protection | No               | Yes                |

---

# Conclusion

SegWit transactions improve efficiency by:

* Separating signature data from transaction data
* Reducing virtual transaction size
* Lowering transaction fees
* Reducing bandwidth requirements
* Eliminating transaction malleability

The comparison clearly demonstrates why SegWit is a significant improvement over traditional Legacy Bitcoin transactions.
