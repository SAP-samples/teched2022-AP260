# Excercise 1 - Intro to Web3/Smart Contrcact Development with Hardhat, Ethereum and Solidity

If you came here to learn about modern smart contract development, you're at the right spot. In this exercise, you will learn how to install and use the latest and most up-to-date tools with the help of a simple starter project. 


## Creating and exploring an new hardhat project
To get started with hardhat, please create a new empty dev directory and open it using Visual Studio Code. If you need a folder name, choose `teched2022-AP260` - that's what we chose. 

Next, let's create a new Node.js project:

```
npm init -y
```

Now, we'll be installing hardhat as a developer dependency:

```
npm install hardhat --save-dev
```

Once this finishes, please verify that you can run `npx hardhat` and follow the steps to create a new TypeScript project - accept all the other defaults. Et voilà - you just created your first hardhat sample project. Take a look around before we continue. 

![Creating a new Hardhat sample project](images/new_hardhat.png "test")

Now install some additional dependencies that we need for the sample project:

```
npm install --save-dev "hardhat@^2.11.2" "@nomicfoundation/hardhat-toolbox@^2.0.0"
```

## Compiling your first sample smart contract

This hardhat sample project comes with a default smart contract, which is in the `contracts/` directory and the full path is `contracts/Lock.sol`. The contract is quite simple and only allows the owner to lock a certain amount of ether (Ethereum's native cryptocurrency) before it can be withdrawn. 

Let's check if we can compile our `Lock.sol` sample contract:

```
npx hardhat compile
```

The above command will attempt to compile all smart contracts in the `contracts` directory. Once the command finishes, you will see the created artifacts in the `artifacts` directory. Feel free to take a look at the generated artifacts.

## Running tests for a smart contract

Next we will check that our testing infrastructure is up and running. The sample project comes with a test for the Lock.sol in the `test` directory: check `tests/Lock.ts` (notice the test is a TypeScript test file, not a .sol = solidity file). 

We're not yet diving into details and simply want to check our testing setup works:

```
npx hardhat test
```

The above will run all tests in the `tests` directory. 

[Testing with Hardhat](images/hardhat_lock_test.png)

Before we conclude this basic "get to know hardhat" part, we will deploy the compiled contract. At this point, we will not use the live, public Ethereum mainnet or one of the testnets, but we will use a local, single node Ethereum network, which hardhat is providing to us.

## Deploying the sample contract to a local test network

You will notice a deployment script for the `Lock.sol` contract in the `scripts` folder. It uses the TypeScript variant of ethers.js, a well-known Ethereum JavaScript library. 

This script first calculates a timestamp being one year in the future, in seconds:

```
  const currentTimestampInSeconds = Math.round(Date.now() / 1000);
  const ONE_YEAR_IN_SECS = 365 * 24 * 60 * 60;
  const unlockTime = currentTimestampInSeconds + ONE_YEAR_IN_SECS;
```

It will then calculate the lockedAmount which needs to be in WEI (the smallest part of an ether):

```
const lockedAmount = ethers.utils.parseEther("1");
```

And finally it deploys the `Lock.sol` contract to the network:
```
  const Lock = await ethers.getContractFactory("Lock");
  const lock = await Lock.deploy(unlockTime, { value: lockedAmount });

  await lock.deployed();
```
 
 Let's run it - and don't worry, the local hardhat network is reset each time if you run the following command:

```
npx hardhat run scripts/deploy.ts
```

You should get a printout for the deployment address of the contract. 

## Summary
Congratulations, you made it through the first exercise! You have successfully used hardhat, one of the best and most current Ethereum development environments out there. With the help of the Hardhat sample project, you have compiled, tested and even deployed a smart contract. Now that you have a basic understanding of how things are rolling, let's dive into the world of NFTs. 

Continue to - [Exercise 2 - Diving into the world of NFTs](../ex2/README.md)

