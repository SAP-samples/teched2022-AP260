[![REUSE status](https://api.reuse.software/badge/github.com/SAP-samples/teched2022-AP260)](https://api.reuse.software/info/github.com/SAP-samples/teched2022-AP260)

# AP260 - Beyond Just an NFT: Unlocking Utility in Smart Contracts

## Description

**YOU DON’T NEED TO CLONE THIS REPOSITORY**.

This repository contains a step-by-step work through for the SAP TechEd 2022 session _AP260 - Beyond Just an NFT: Unlocking Utility in Smart Contracts_.

## Overview

This workshop introduces attendees to the basics of modern smart contract development for Ethereum Virtual Machine (EVM) based smart contracts. The workshop is split into three exercises that build upon each other:

- **Part 1** introduces the participants to a basic sample project using the Hardhat development environment. Here, we will make sure that your system is setup correctly. Further you will get a basic understanding how smart contracts are created, tested, and deployed to the local Hardhat test network.
- In **Part 2** we dive into the world of specialized smart contracts, here ERC-721 contracts which are used for NFTs. You will create your first fully functional NFT smart contract, deploy it to a live test network, and interact with it (e.g. MetaMask within the browser).
- Finally, **Part 3** will teach you how smart contracts can be modified to add features and functionality beyond the standard features. While we will discuss SAP Customer Experience related functionalities in our presentation, you will work hands-on on a smart contract that can be used for event ticketing. This should give you an understanding about NFTs and utilities.

## Requirements

Let's first run through some requirements, which need to be installed in your local development environment.

- Make sure [Microsoft Visual Studio Code](https://code.visualstudio.com/) is installed.
- We are using Hardhat, a NodeJs-based Ethereum development environment. That's why you need to check if you have NodeJs (preferably the 16.17.1 LTS) installed.
- To verify the NodeJs version open Visual Studio Code and open inside of Visual Studio Code new Terminal (Menu Terminal / New Terminal). Type `node --version` which should print the recommended version.

**For the workshop, we provide all necessary items (e.g. API keys, funded Ethereum account). As participant you do not need to waste your time by creating these accounts nor do they need to use their own private accounts.**

All good? Congrats. Let's dive right in.

<details>
  <summary>How to setup your system at home</summary>

Note to non-workshop, self-paced / study at home participants: You will notice that you will require API keys (e.g. Etherscan, Alchemy) and a funded Ethereum test account at some point.

 If you follow along outside of a workshop, plan for some extra time to create accounts and request API keys for these services:

- [Alchemy](https://www.alchemy.com/) - well-known web3 development platform which gives us access to live blockchain nodes (we use the Polygon Mumbai test network)
- [Etherscan](https://etherscan.io/) - the Ethereum Blockchain Explorer which we use to interact and verify our smart contracts
- [MetaMask](https://metamask.io/), a well-known crypto wallet which is installed as a plugin to web browsers such as Chrome or Edge. You will need to export the private key for the Hardhat config of one of the accounts. If you need to fund your account with Mumbai test MATIC, we recommend to use [mumbaifaucet.com](https://mumbaifaucet.com/) which requires an Alchemy account.

</details>

## Exercises

The workshop is based on three exercises. Each exercise build up on the other:

- [Exercise 1 - Intro to Web3/Smart Contract Development with Hardhat, Ethereum and Solidity](exercises/ex1/)
- [Exercise 2 - Diving into the world of NFTs](exercises/ex2/)
- [Exercise 3 - Adding utility to your smart contracts](exercises/ex3/)

## How to obtain support

Support for the content in this repository is available during the actual time of the online session for which this content has been designed. Otherwise, you may request support via the [Issues](../../issues) tab.

## License

Copyright (c) 2022 SAP SE or an SAP affiliate company. All rights reserved. This project is licensed under the Apache Software License, version 2.0 except as noted otherwise in the [LICENSE](LICENSES/Apache-2.0.txt) file.
