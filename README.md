# AP260 - Beyond Just an NFT: Unlocking Utility in Smart Contracts

## Test Remote Desktop
- Install node with command line tools, then open node command prompt and start code from there
- install git for windows
- `npm init`
- `npm install --save-dev hardhat`
- `npx hardhat`
- Create Typescript project, accept all defaults
- `npm install --save-dev "hardhat@^2.11.2" "@nomicfoundation/hardhat-toolbox@^2.0.0"`
- `npx hardhat` (again to see options)
- check the Lock.sol in the contracts folder
- `npx hardhat compile`
- look at test folder
- `npx hardhat test`
- next deploy to local hardhat network 
- `npx hardhat run scripts/deploy.ts`

--- START ERC721 - wizard.openzeppelin.com, basic ERC721

- copy to SuperNFT.sol
- `npx hardhat compile` -> error -> we need openzeppelin contracts
- `npm install @openzeppelin/contracts --save`
- `npx hardhat compile` -> all good
- create a scripts/deploySuperNFT.ts file... content below

```
import { ethers } from "hardhat";

async function main() {

  const NFT = await ethers.getContractFactory("SuperNFT");
  const nft = await NFT.deploy();

  await nft.deployed();

  console.log(`SuperNFT deployed to  ${nft.address}`);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```
- npx hardhat run scripts/deploySuperNFT.ts

- config Change to deploy to live test network

```
import { ethers } from "hardhat";

async function main() {
  const [deployer] = await ethers.getSigners();

  console.log("Deploying contracts with the account:", deployer.address);

  console.log("Account balance:", (await deployer.getBalance()).toString());

  const NFT = await ethers.getContractFactory("SuperNFT");
  const nft = await NFT.deploy();

  console.log("SuperNFT address:", nft.address);
}

main()
  .then(() => process.exit(0))
  .catch((error) => {
    console.error(error);
    process.exit(1);
  });
  ```

- add goerli (alchemy and private key required!!! TODO)
- install/init  metamask?

hardhat.config.ts
```
import { HardhatUserConfig } from "hardhat/config";
import "@nomicfoundation/hardhat-toolbox";

const GOERLI_PRIVATE_KEY = "xxx";

const config: HardhatUserConfig = {
  solidity: "0.8.17",
  networks: {
    goerli: {
      url: `https://eth-goerli.g.alchemy.com/v2/LBB-4vNxpUYZB9mKx4F81hlRsCT-6oqU`,
      accounts: [GOERLI_PRIVATE_KEY]
    }
  }
};

export default config;
```

  - npx hardhat run scripts/deploySuperNFT.ts --network goerli

- still error, no funds
- fund public address (check metamask, already funded? )

- Take note of contract address: 0xc3D9104bd9FE777d8cD5a7Dfd1FbC01C167Ef44C
- Check on etherscan (via browser)
- https://goerli.etherscan.io/address/0xc3D9104bd9FE777d8cD5a7Dfd1FbC01C167Ef44C
- verify contract

- API Key from Etherscan required... TODO
- teched api key HR5G73FSVD6ACG7WVNIZFRDAURUSZ66HPJ (sven's private account...)
- add to hardhat.config.ts

```
  etherscan: {
    apiKey: "HR5G73FSVD6ACG7WVNIZFRDAURUSZ66HPJ",
  },
```

- npx hardhat verify --network goerli <address>
- npx hardhat verify --network goerli 0xc3D9104bd9FE777d8cD5a7Dfd1FbC01C167Ef44C
- back to browser, check it's verified. connect to metamask... 
- intro to metadata, ipfs, etc. 
- call safemint vai etherscan.
- sample metadata (presales academy, MAKE NEW FOR TECHED): ipfs://bafkreih54qb2hb2lawl2zthkz3ijoj6zwzst3ziyblkpf3qxn33onqsh2a
- check transaction in browser
- check on https://testnets.opensea.io/

- NEXT back to vs code, augment NFT, redeploy or play locally?




## Description

This repository contains the material for the SAP TechEd 2022 session called Session ID - Session Title.  

## Overview

This session introduces attendees to...

## Requirements

The requirements to follow the exercises in this repository are...

## Exercises

Provide the exercise content here directly in README.md using [markdown](https://guides.github.com/features/mastering-markdown/) and linking to the specific exercise pages, below is an example.

- [Getting Started](exercises/ex0/)
- [Exercise 1 - First Exercise Description](exercises/ex1/)
    - [Exercise 1.1 - Exercise 1 Sub Exercise 1 Description](exercises/ex1#exercise-11-sub-exercise-1-description)
    - [Exercise 1.2 - Exercise 1 Sub Exercise 2 Description](exercises/ex1#exercise-12-sub-exercise-2-description)
- [Exercise 2 - Second Exercise Description](exercises/ex2/)
    - [Exercise 2.1 - Exercise 2 Sub Exercise 1 Description](exercises/ex2#exercise-21-sub-exercise-1-description)
    - [Exercise 2.2 - Exercise 2 Sub Exercise 2 Description](exercises/ex2#exercise-22-sub-exercise-2-description)

  
**OR** Link to the Tutorial Navigator for example...

Start the exercises [here](https://developers.sap.com/tutorials/abap-environment-trial-onboarding.html).

**IMPORTANT**

Your repo must contain the .reuse and LICENSES folder and the License section below. DO NOT REMOVE the section or folders/files. Also, remove all unused template assets(images, folders, etc) from the exercises folder. 

## How to obtain support

Support for the content in this repository is available during the actual time of the online session for which this content has been designed. Otherwise, you may request support via the [Issues](../../issues) tab.

## License
Copyright (c) 2022 SAP SE or an SAP affiliate company. All rights reserved. This project is licensed under the Apache Software License, version 2.0 except as noted otherwise in the [LICENSE](LICENSES/Apache-2.0.txt) file.
