# AP260 - Beyond Just an NFT: Unlocking Utility in Smart Contracts

## TODO
- ex2 create metadata and image on IPFS and change info for 
- ex3 valid/redeemed IPFS metadata and change in ex3


## Description

This repository contains the material for the SAP TechEd 2022 session called AP260 - Beyond Just an NFT: Unlocking Utility in Smart Contracts.  

## Overview

This workshop introduces attendees to the basics of modern smart contract development for Ethereum Virtual Machine (EVM) based smart contracts. The workshop is split into three exercises that build upon each other:

- **Part 1** introduces the participants to a basic sample project using the Hardhat development environment. Here, we will make sure that your system is correctly setup and leave you with a basic understanding how smart contracts are created, tested and deployed to a local test network. 
- In **Part 2** we dive into the world of specialized smart contracts, here ERC-721 contracts which are used for NFTs. You will create your first fully functional NFT smart contract, deploy it to a live test network and interact with it via tools such as MetaMask from a browser. 
- Finally, **Part 3** will teach you how smart contracts can be modified to add features and functionality beyond the standard features. While we will discuss SAP Customer Experience related functionalities in our presentation, you will work hands-on on a smart contract that can be used for event ticketing.  

## Requirements
Let's first run through some requirements, which need to be installed in your local development environment. 
- Make sure [Microsoft Visual Studio Code](https://code.visualstudio.com/) is installed. 
- We'll be using hardhat, a nodejs-based Ethereum development environment. That's why you need to check if you have Node.js (preferably the 16.17.1 LTS) installed. Try this out via Visual Studio Code: open a new Terminal and type `node --version` which hopefully prints the recommended version.

Note to non-workshop, self-paced / study at home participants: you will notice that you will require API keys (Etherscan, Alchemy) and a (funded) Ethereum test account at some point. **For the workshop, we provide all these items so the participants do not waste time creating these accounts nor do they need to use their own private accounts.** If you follow along outside of a workshop, plan for some extra time to create accounts and request API keys for these services:

- [Alchemy](https://www.alchemy.com/) - well-known web3 development platform which gives us access to live testnet nodes
- [Etherscan](https://etherscan.io/) - the Ethereum Blockchain Explorer which we use to interact and verify our smart contracts
- [MetaMask](https://metamask.io/), a well-known crypto wallet which is installed as a plugin to web browsers such as Chrome or Edge. You will need to export the private key for the hardhat config of one of the accounts. If you need to fund your account with Goerli test Ether, we recommend to use [goerlifaucet.com](https://goerlifaucet.com/) which requires an Alchemy account. 

All good? Congrats. Let's dive right in.

## Exercises

- [Excercise 1 - Intro to Web3/Smart Contrcact Development with Hardhat, Ethereum and Solidity](exercises/ex1/)
- [Excercise 2 - Diving into the world of NFTs](exercises/ex2/)
- [Excercise 3 - Adding utility to your smart contracts](exercises/ex3/)
    

**IMPORTANT**

Your repo must contain the .reuse and LICENSES folder and the License section below. DO NOT REMOVE the section or folders/files. Also, remove all unused template assets(images, folders, etc) from the exercises folder. 



## NFTs and Utility



## How to obtain support

Support for the content in this repository is available during the actual time of the online session for which this content has been designed. Otherwise, you may request support via the [Issues](../../issues) tab.

## License
Copyright (c) 2022 SAP SE or an SAP affiliate company. All rights reserved. This project is licensed under the Apache Software License, version 2.0 except as noted otherwise in the [LICENSE](LICENSES/Apache-2.0.txt) file.
