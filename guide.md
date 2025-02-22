#  Developer Guide for PoCS

## Introduction

This Substrate Node is an adaptation of the substrate-stencil framework, customized to integrate the Proof of Contract Stake (PoCS) protocol. The PoCS protocol introduces a developer-centric consensus mechanism that combines elements of Proof-of-Stake (PoS) and Proof-of-Work (PoW), leveraging smart contract activity as a core component of staking and network security.

The node integrates modified versions of pallet_contracts and pallet_staking, enabling contract staking features while maintaining interoperability with Substrate's existing NPoS-BABE-GRANDPA consensus infrastructure. This approach aligns developer incentives with network security, creating a secure and developer-focused blockchain environment.

The node template also serves as a starting point for experimentation and customization, ensuring compatibility with the Polkadot ecosystem and facilitating cross-chain interoperability through parachains. It is designed to support features like contract-based reputation scoring, contract delegation, validator selection, and reward distribution, forming the foundation for a robust PoCS-enabled blockchain.

### What is PoCS ?

Proof of Contract Stake (PoCS) is an innovative, developer-centric blockchain consensus mechanism designed to place smart contracts at the core of network security and functionality. It introduces a staking system that uses contract gas history to select block producers, integrating elements of both Proof-of-Work (PoW) and Proof-of-Stake (PoS).

Key features of PoCS include:

- **Code-Mining:** Incentivizes developers by aligning their participation with network security, making smart contract creators vital to the consensus process.
- **Stake Scoring:** Considers factors like contract age, reputation, and gas utilization to determine staking power, ensuring fairness and resistance to collusion.
- **Mitigation of 'Nothing at Stake':** Introduces non-fungible, non-transferable staking units, addressing a common weakness in traditional PoS systems.
- **Defense Against Stake Accumulation Attacks:** Implements a time-constrained and patterned stake accumulation process, making attacks costly and easily detectable.

Overall, PoCS ensures a secure, fair, and developer-oriented blockchain environment, creating a new paradigm in consensus design.

### How Contract Staking works ?

Contract Staking integrates the functionalities of pallet-staking and pallet-contracts to enable smart contracts to actively participate in the blockchain consensus mechanism. Below is a detailed summary of how the process works:

![PoCS_working](https://shorturl.at/TLNxR)

**Stake Score Calculation:** Developers deploy smart contracts with a `stake_score`, derived from the contract's execution time (refTime) and reputation.

**Validator Nomination:** Developers nominate a validator for their contract using the `update_delegate()` extrinsic. Contracts must meet a minimum reputation (set at 10 during development, adjustable) to nominate a validator.

**Stake Purge on Delegate Change:** Changing a contract's delegated validator resets its stake_score to 0.

**Validator Activation:** Validators require a minimum number of delegators (i.e., bonded contracts) to start validating. The min_delegates threshold is set at 3 during development, adjustable.

This system ensures that only reputable contracts and sufficiently supported validators participate in consensus.

Extended details available in [PoCS-Research Document](https://auguth.org/PRESS/pocs).

### Why PoCS ?

Proof of Contract Stake (PoCS) addresses a critical gap in blockchain technology by introducing a developer-centric consensus mechanism that places smart contracts at the core of network security. While traditional consensus mechanisms like Proof-of-Stake (PoS) and Proof-of-Work (PoW) prioritize validators and miners, PoCS shifts the focus to smart contract creators, aligning their interests with the security and functionality of the network.

Key Benefits of PoCS:

- **Developer Incentivization:** PoCS uses a unique stake scoring system based on contract execution, age, reputation, and gas utilization to reward active developers.
- **Enhanced Security:** By tying consensus to contract activity, PoCS mitigates risks like 'nothing at stake' attacks and stake accumulation through time-constrained mechanisms.
- **Fairness and Decentralization:** PoCS integrates contract activity and validator reputation into staking, creating a fairer environment resistant to collusion attacks.
- **Interoperability and Innovation:** Built on Substrate, PoCS is designed to integrate seamlessly into the Polkadot ecosystem, enabling cross-chain compatibility and a robust developer framework.

PoCS represents a paradigm shift in blockchain consensus, transforming smart contract creators into key stakeholders while fostering a secure, scalable, and inclusive network.

## Project Setup 

In Substrate, most of the developer tools are written in the Rust programming language. Steps to set-up the rust tool chain is :

Before installing Rust, ensure your system has the necessary packages. On Linux, install the following dependencies:

    ``` bash
    sudo apt install --assume-yes git clang curl libssl-dev llvm libudev-dev make protobuf-compiler
    ```

### Installing Rust

1. **Install Rust Toolchain**

    Install Rust using the `rustup` toolchain manager:

    ```bash
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
    ```

2. **Set Up Environment**

    Add Rust to the Shell Path:

    ```bash
    source $HOME/.cargo/env
    ```

3. **Verify Installation**

    Ensure Rust is correctly installed:

    ```bash 
    rustc --version
    ```

4. **Configure Rust Toolchain**

- Install the specific nightly version for the project:

    ``` bash
    rustup install nightly-2023-12-21
    ```

- Add the `wasm32-unknown-unknown` target for this nightly version:

    ```bash
    rustup target add wasm32-unknown-unknown --toolchain nightly-2023-12-21
    ```

- Set the nightly version as the default for this project:

    ```bash
    rustup override set nightly-2023-12-21
    ```

5. **Update and Verify Rust Toolchains:**

    ```bash
    rustup show
    rustup +nightly-2023-12-21 show
    ```

## Build & Run PoCS Node

1. **Clone the repository from GitHub:**

   ```bash
   git clone https://github.com/auguth/pocs
   ```

2. **Run the Rust Setup Script**

   ```bash
   chmod +x setup.sh && ./setup.sh
   ```

3. **Build the project in release mode:**
   
   ```bash
   cargo build --release
   ```
4. **Run the executable with the specified configuration:**
   
    ```bash
    ./target/release/pocs --dev
    ```
5.  **Use [Polkadot-JS-App](https://polkadot.js.org/apps/) and [Contracts UI](https://contracts-ui.substrate.io/) to interact with the Node. Refer to [Testing Guide.md](/TESTING-GUIDE.md) for extended information.**

## Working of PoCS 

The Proof of Contract Stake (PoCS) mechanism innovatively combines smart contract interactions with staking, aligning developer contributions with network security. PoCS operates by integrating key Substrate pallets, namely `pallet_contracts` and `pallet_staking`, and is further extended through a validator contract to manage rewards and validator behavior.

### Pallets Used in PoCS

1. Pallet Contracts

- Handles smart contract interactions, including execution, reputation management, and staking score computation.
- Documentation: [Pallet Contracts Documentation](https://auguth.github.io/pocs/target/doc/pallet_contracts/index.html)

2. Pallet Staking

- Implements staking logic, allowing validators to be selected based on contract stake scores.
- Documentation: [Pallet Staking Documentation](https://auguth.github.io/pocs/target/doc/pallet_staking/)


### Contract Staking (pallet_contracts)

The contract staking mechanism revolves around smart contracts deployed by developers. Each contract is associated with a stake score, determined by the following factors:

- Execution Time: Reflects the computational cost of contract calls.
- Reputation: Based on user interactions, frequency of calls, and gas utilization.

**Key Features of Contract Staking:**

- Contracts with sufficient reputation can nominate validators using the `update_delegate()` extrinsic.
- Stake scores reset when contracts change their delegated validators.
- Validators must meet a minimum threshold of nominating contracts to begin validating.

For implementation details, visit the repository: [Contract Staking (pallet_contracts)](https://shorturl.at/zDocL)

### Validator Reward Contract

#### Overview

The Validator Reward Contract is an integral component of the PoCS system. It is designed to manage the distribution of rewards to validators by leveraging smart contract capabilities provided by ink! and integrating closely with PoCS’s unique staking mechanism. This contract enforces a cooldown period between reward claims to ensure fair and secure reward distribution and lays the groundwork for more sophisticated reward logic based on validator performance and smart contract activity.

#### Ink! Environment Setup

Before working with the Validator Reward Contract, ensure your development environment meets the following requirements:

- Rust & C++ Compiler:

    In addition to Rust, installation requires a C++ compiler that supports C++17. Modern releases of gcc and clang, as well as Visual Studio 2019+, should work.

**Installation Steps:**

1. Install Rust Source:

    ``` rust
    rustup component add rust-src
    ```

2. Install `cargo-contract`:

    ``` rust
    cargo install --force --locked cargo-contract
    ```

#### How to Use the Contract

1. Build and Deploy:

    - Clone your repository containing the validator reward contract code.
    - Run the setup script if applicable:
        ``` rust
        chmod +x setup.sh && ./setup.sh
        ```
    - Build the contract in release mode using cargo-contract:
        ``` rust
        cargo contract build --release
        ```
    - Deploy the contract to your local or test network using the Polkadot-JS-App or Contracts UI:
        - Upload the WASM binary.
        - Initialize the contract by providing the required constructor parameter (owner).

#### Features and Future Extensions

- **Cooldown Mechanism:**
The contract enforces a cooldown period (e.g., requiring at least 10 blocks between successive claims) to prevent abuse and ensure fair reward distribution.

- **Reward Calculation:**
Currently, rewards are fixed. Future improvements can integrate more nuanced calculations that factor in:

    - Contract reputation and activity.
    - Stake and staking score based on smart contract interactions.
    - Dynamic adjustment of the reward amount based on network conditions.

- **Integration with PoCS:**

    - **Validator Selection:** Validators in PoCS are selected based on contract staking and reputation. This reward contract complements that system by providing incentives.
    - **Interoperability:** Built on the Substrate framework, this contract can easily interact with other pallets (like pallet_contracts and pallet_staking) ensuring a robust, cross-chain compatible ecosystem.
    - **Scalability:** The modular design allows developers to extend the reward logic as the PoCS protocol evolves, enabling a more dynamic incentive structure.

- **Developer-Centric Design:**
By integrating smart contract activity into the consensus process, PoCS and this reward contract align developer interests with network security, fostering a more engaged and innovative ecosystem.