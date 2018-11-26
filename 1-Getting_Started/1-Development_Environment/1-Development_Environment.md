## 1.1. Introduction

This guide is perfect for learning the ins-and-outs of EOSIO from the perspective of a developer.

### What you'll learn

Few samples of what you'll learn:

- How to quickly spin up a node
- Manage wallets and keys
- Create Accounts
- Write some contracts
- Compilation and ABI
- Deploy contracts

### EOSIO Suits

nodeos, cleos, keosd, eosio.cdt, eosio.constracts

### C/C++ Experience

EOSIO based blockchains execute user-generated applications and code using WebAssembly (WASM). WASM is an emerging web standard with widespread support of Google, Microsoft, Apple, and industry leading companies.

At the moment the most mature toolchain for building applications that compile to WASM is clang/llvm with their C/C++ compiler. For best compatibility, it is recommended that you use the EOSIO C++ toolchain.

Other toolchains in development by 3rd parties include: Rust, Python, and Solidity. While these other languages may appear simpler, their performance will likely impact the scale of application you can build. We expect that C++ will be the best language for developing high-performance and secure smart contracts and plan to use C++ for the foreseeable future.

## Before You Begin

### Step 1: Download Dependencies

- [Download Docker](https://www.docker.com/get-started) - The tutorial is going to be using Docker. For you to get started as quickly as possible this is the best option at the moment. Building from source is an option, but will set you back an hour or more and you may encounter build errors.

### Step 2: Setup and Use a development directory, stick to it

You're going to need to pick a directory to work from, it's suggested to create a contracts directory somewhere on your local drive.

```bash
mkdir contracts
cd contracts
```
