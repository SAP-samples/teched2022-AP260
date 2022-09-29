# AP260 - Beyond Just an NFT: Unlocking Utility in Smart Contracts

## Description

This repository contains the material for the SAP TechEd 2022 session called AP260 - Beyond Just an NFT: Unlocking Utility in Smart Contracts.  

## Overview

This workshop introduces attendees to the basics of modern smart contract development for Ethereum Virtuam Machine (EVM) based smart contracts. The workshop is split into three parts:

- **Part 1** introduces the participants to a basic sample project using the Hardhat development environment. Here, we will make sure that your system is correctly setup and leave you with a basic understanding how smart contracts are created, tested and deployed to a local test network. 
- In **Part 2** we dive into the world of specialized smart contracts, here ERC-721 contracts which are used for NFTs. You will create your first fully functional NFT smart contract, deploy it to a live test network and interact with it via tools such as MetaMask from a browser. 
- Finally, **Part 3** will teach you how smart contracts can be modified to add features and functionality beyond the standard features. While we will discuss SAP Customer Experience related functionalities in our presentation, you will work hands-on on a smart contract that can be used for event ticketing.  

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

## Intro to Web3/Smart Contrcact Developmennt with Hardhat, Ethereum and Solidity
If you came here to learn about modern smart contract development, you're at the right spot. In this section of the workshop, you will learn how to install and use the latest and most up-to-date tools with the help of a simple starter project. 

This is *not exactly* the super exciting part, such as NFTs and new utilities, you came for. But don't worry, we'll spicy up the coolness level pretty soon and then start exploring the world of NFTs in the next part.

### Prerequisites
Let's first run through some pre-requisites, which need to be installed in your local development environment. 
- Make sure [Microsoft Visual Studio Code](https://code.visualstudio.com/) is installed. 
- We'll be using hardhat, a nodejs-based Ethereum development environment. That's why you need to check if you have Node.js (preferably the 16.17.1 LTS) installed. Try this out via Visual Studio Code, open a new Terminal and type `node --version` which hopefully prints the recommended version.

TODO GIT? not really needed? All the we need is this readme? 

All good? Congrats. Let's dive right in.

### Creating and exploring an new hardhat project
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

Now install some additional dependencies that we need for the sample project:

```
npm install --save-dev "hardhat@^2.11.2" "@nomicfoundation/hardhat-toolbox@^2.0.0"
```

This hardhat sample project comes with a default smart contract, which is in the `contracts/` directory and the full path is `contracts/Lock.sol`. The contract is quite simple and only allows the owner to lock a certain amount of ether (Ethereum's native cryptocurrency) before it can be withdrawn. 

Let's check if we can compile our `Lock.sol` sample contract:

```
npx hardhat compile
```

The above command will attempt to compile all smart contracts in the `contracts` directory. Once the command finishes, you will see the created artifacts in the `artifacts` directory. 

Next we will check that our testing infrastructure is up and running. The sample project comes with a test for the Lock.sol in the `test` directory: check `tests/Lock.ts` (notice the test is a TypeScript test file, not a .sol = solidity file). 

We're not yet diving into details and simply want to check our testing setup works:

```
npx hardhat test
```

The above will run all tests in the `tests` directory. 

Before we conclude this basic "get to know hardhat" part, we will deploy the compiled contract. At this point, we will not use the live, public Ethereum mainnet or one of the testnets, but we will use a local, single node Ethereum network, which hardhat is providing to us.

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
Half-way understood? Let's run it - and don't worry, the local hardhat network is reset each time if you run the following command:

```
npx hardhat run scripts/deploy.ts
```

You should get a printout for the deployment address of the contract. Congrats! This concludes a first walk-through the hardhat development environment. Let's now get more serious and create our first NFT!

## Get started with NFTs - from contract creation to your first NFT minted on testnet
We're now ready to dive into the world of NFTs. On Ethereum, NFTs are represented via smart contracts, interacted with via the contract's functions and the uniqueness of an NFT is guaranteed by state kept on a public blockchain. By the end of this part, you will have deployed your own NFT smart contract to a public testnet and you will be able to check out your NFT on OpenSea's Testnet. 

### Creating a new NFT contract
To create an NFT, you will first have to deploy a smart contract with adheres to the ERC-721 standard. You can see a full list of required functions [here](http://erc721.org/).  While we could start from scratch with an almost empty `.sol` File, we'll not re-invent the wheel here. Instead, we will make use of the excellent [OpenZeppelin smart contract wizard, which we kindly ask you to open in your browser now](https://wizard.openzeppelin.com/).OpenZeppelin contracts is a library of community-reviewed smart contracts.

Let's configure a new ERC-721 smart contract:

- Switch to the ERC721 tab in the wizard
- Your're free to choose a token name and symbol, but don't forget to change these names later in the code. To keep things simple, we recommend to choose `TechedToken` as the name and  `TECH` as the symbol. 
- We don't need a base URI for our fist NFT smart contract... keep the field empty.
- For features, choose `mintable` and `auto increment ids`. As owners of this future deployed contract, we want to be able to mint new NFTs and the internal uninque TokenID shall be automatically incremented.
- Please also enable `burnable` to enable token holders to destroy their tokens and `uri storage` to be able to specify fresh metadata for each NFT that gets minted.
- For the remaining settings, just keep the defaults. 

Click the `Copy to clipboard` button and switch to Visual Studio Code. Here, in the `contracts` folder, create a new file called `TechedToken.sol` and copy over the contents. 

We'll now immediately try to compile the new contract:

```
npx hardhat compile
```

Whoa, what's happening? Do you also see some errors? It seems we forgot to include OpenZeppeli contracts to our hardhat development environment. Let's fix that:

```
npm install @openzeppelin/contracts --save-dev
```

Now try compiling again - `npx hardhat compile` and hardhat should happily find all imports. 

As we want to deploy our compiled contract, we have to create another deployment script. In the `scripts` folder, create a new `deployTeched.ts` TypeScript file. The content is very similar to the default deployment script, but of course we reference our new contract:

```
import { ethers } from "hardhat";

async function main() {

  //make sure to sue the correct Token name for the ContractFactory!
  const TechedToken = await ethers.getContractFactory("TechedToken");
  const techedToken = await TechedToken.deploy();

  await techedToken.deployed();

  console.log(`TechedToken deployed to  ${techedToken.address}`);
}

// We recommend this pattern to be able to use async/await everywhere
// and properly handle errors.
main().catch((error) => {
  console.error(error);
  process.exitCode = 1;
});
```
We're now ready to deploy (still to our local hardhat network), let's try that:

```
npx hardhat run scripts/deployTeched.ts
```

All good? Then let's add a new network to our hardhat configuration to be able to deploy to a public ethereum testnet. The one we have chosen is the Goerli testnet. 

To add this network to our hardhat configuration, please open the file `hardhat.config.ts` and add the network configuration for `Goerli` - just copy the code below. We will next discuss a few things we need to change in this file.

```
import { HardhatUserConfig } from "hardhat/config";
import "@nomicfoundation/hardhat-toolbox";

const GOERLI_PRIVATE_KEY = "xxx";
const ALCHEMY_KEY = "xxx"

const config: HardhatUserConfig = {
  solidity: "0.8.17",
  networks: {
    goerli: {
      url: `https://eth-goerli.g.alchemy.com/v2/${ALCHEMY_KEY}`,
      accounts: [GOERLI_PRIVATE_KEY]
    }
  }
};

export default config;
```
TODO my key / alchemy is LBB-4vNxpUYZB9mKx4F81hlRsCT-6oqU

There are two important changes you need to make to the above template:
- GOERLI_PRIVATE_KEY: you need your own, funded ethereum account to be able to deploy to a live network. This is why we prepped a couple of accounts and funded them with test either just for this workshop. 
- ALCHEMY_KEY: deploying to a public network requires access to a network node. Alchemy is one of the well-known providers of Ethereum network nodes and you will be able to use an existing API key to get access to Ethereum via their nodes.

DO THIS: Lookup your table ID and then check with the workshop owners to download and lookup the information above. Once you have your private key and the Alchemy API key, replace `xxx` in the `hardhat.config.ts` file with this information. 

Before we deploy to the public Goerli testnet in a second, we just make one optional change to the `deployTeched.ts` file. Please add these few lines to the script top:

```
...
async function main() {
  const [deployer] = await ethers.getSigners();
  console.log("Deploying contracts with the account:", deployer.address);
  console.log("Account balance:", (await deployer.getBalance()).toString());
...
```

The lines above are not required, but they will help you to understand what's going on. Via `getSigners()` we're accessing the account you added to the `hardhat.config.ts`. We're also printing the public address of that account and the current balance, which might be useful for trouble-shooting. 

Ready? 

```
npx hardhat run scripts/deployTeched.ts --network goerli
```

If all went well, you have now successfully deplpoyed your first NFT smart contract to the Goerli testnet. *Please take note of the contract address that is printed in the logs.*

```
TODO add sample deployment output
```

At this point you have deployed the NFT smart contract, but you have not yet created a single NFT. To create an NFT, we'll have to interact with the functions of the smart contract. While we could do this from ethers.js and our hardhat dev environment, we want to show you another neat way to interact with contracts: the etherscan explorer.

Open a browser and navigate to [goerli.etherscan.io](https://goerli.etherscan.io/). In the big search bar, type type the address of the contract you took note of earlier. Once the page has loaded, click the `Contract` tab which is a bit below. 

Bummer. You will now see the compile contract, which is not really fun to interact with. The trick is called verification. We will now verify our newly deployed smart contract and then be able to interact with the smart contract via the etherscan.io web interface. 

As verification is a service of etherscan, you would normally require another API key for their API. Luckily, you will find a ready to use etherscan API key in the same download as with the account private key and the Alchemy API key. 

DO THIS: check for the etherscan API key and then add these lines with the correct key to the `hardhat.config.ts` file:

```
  etherscan: {
    apiKey: "ETHERSCAN API KEY HERE",
  },
```

Verification is now a piece of cake - but you have to remember the contract address:

```
npx hardhat verify --network goerli <contract address>
```

If this went well, refresh the tab in the web browser - you should be able to see the read/write sections and you're now ready to interact with the live contract... almost!

By default, your browser is not able to send transaction to an ethereum (test) node. For this to happen, we need to install the MetaMask wallet which in combination with a library called web3.js (it's used by etherscan.io on their web pages) is able to send our transactions to the network.

DO THIS: Visit [metamask.io](https://metamask.io/) and click the download button. Follow the steps to install MetaMask as a browser plugin. Next, click the MetaMask plugin (top right in the browser and in the add-ons section) and choose "TODO restore from seed phrase". You will find the seed phrase/words in the same download as before. This will import the same account used in the deployment script into your MetaMask wallet in the browser. 

TODO images

As we are using the Goerli test network, please now switch the network used for MetaMask via the top drop down box to the Goerli Test Network. For this, you need to enable the test networks by clicking the "Show/hide test networks". 

Once MetaMask is ready, reload the etherscan.io page with your smart contract. In the bottom tab, be sure to have switched to Contract (which now has the green verification check mark) and click on `Write Contract` to see the write functions of your smart contract. Next, click `Connect to Web3`. This will pop up the MetaMask plugin and will need to confirm the connection to this web page. 

TODO images/animated gif?

We will next create our first NFT by calling the safeMint function - click on safeMint in the function list on etherscan.io. This function requires two inputs: 

- to: the address the NFT should be owned by
- uri: a link to the metadata of the NFT

TODO - NFT Intro / Info via presentaiton at this point - info about metadata, ipfs, etc.

To mint your first NFT, let's choose your public address (look it up via MetaMask) for the `to` field. For the uri to the MetaData, you can choose this IPFS link, which we have prepared beforehand: `TODO ipfs://sdfasdfdasfasdfsdf`.

Click the `Write` button and confirm the transaction via MetaMask. Now check the transaction and it's status by using the link just next to the `Write` button. 

At this point, you are now able to verify that all went well by using one of the public NFT marketplaces such as Opensea.io. As we have used the Goerli Ethereum testnet, we'll have to switch to the testnet version of Opensea.io, too - which is [testnets.opensea.io](https://testnets.opensea.io/). Search for your smart contract... with a bit of luck (and time, it often takes a few minutes) you will be able to find your fresh NFT on OpenSea.

Still with us? Ready for more smart contract development? Let's take this one step further and discuss how we can add real utility to our ERC-721 NFT smart contract.

## NFTs and Utility

Let's first create an exact copy of the existing NFT contract code, so copy over `TechedToken.sol` to a new contract called `TicketToken.sol`. Open the new contract and change the contract name inside the file also to `TicketToken` as well as the name inside the constructor as below:

```
...
contract TicketToken is ERC721, ERC721URIStorage, Ownable {
...
constructor() ERC721("TicketToken", "TICKET") {}
...
```

Let's now discuss what features our new TicketToken contract should have:
- as our TicketToken NFTs represent tickets to events, we want to be able to store the validity of each ticket. Once a ticket has been redeemed, we want to mark it used. 
- once a ticket has been redeemed, it cannot be transferred any more. 
- the appearance of our TicketToken NFT should change based on that status of the validity. Before and event and before a ticket was redeemed, it might have the look of a typical paper ticket. Once redeemed, the nft image should change into another picture, remembering the customer of the great event. 

To store the validity of each NFT, we need to introduce a new mapping inside our smart contract. Put the following lines just above the constructor of the smart contract:

```
//mapping from tokenID to bool (validity true/false)
mapping(uint256 => bool) private _validity;
```

And we also want to be able to change the appearance of our NFT based on the validity state, so we need to add another mapping for the second URI that we need to store. Add this also anywhere above the constructor:

```
//mapping for tokenURIs for redeemed tickets
mapping(uint256 => string) private _redeemedTokenURIs;
```

Before we add the functionality to the functions of the NFT, let's now start to create a new test in the `tests` folder. Start by copying an existing test and rename it to `TicketToken.ts` inside the `test` folder. Below is a template wiht an initial test for the token name and symbol that you can use:

```
import { time, loadFixture } from "@nomicfoundation/hardhat-network-helpers";
import { anyValue } from "@nomicfoundation/hardhat-chai-matchers/withArgs";
import { expect } from "chai";
import { ethers } from "hardhat";

describe("TicketToken", function () {

  async function deployFixture() {
   
    // Contracts are deployed using the first signer/account by default
    const [owner, firstAccount, secondAccount] = await ethers.getSigners();

    const Token = await ethers.getContractFactory("TicketToken");
    const token = await Token.deploy();

    return { token, owner, firstAccount, secondAccount };
  }

  describe("Deployment", function () {
    const _name = "TicketToken";
    const _symbol = "TICKET";

    it("Should have the right name and symbol", async function () {
      const { token } = await loadFixture(deployFixture);

      expect(await token.name()).to.equal(_name);
      expect(await token.symbol()).to.equal(_symbol);
    });

  });

});

```

You should be able to run this basic test via `npx hardhat test`.

As the token minting function - safeMint - will now have to take two token URIs, one for the valid version and one for the redeemed version, we have to *replace* the safeMint funcion. To accomodate for the storage of the second URI, we simply created another function. Add the following code to the `contracts/TicketToken.sol` file.

```

    function safeMint(
        address to,
        string memory validURI,
        string memory redeemedURI
    ) public onlyOwner {
        uint256 tokenId = _tokenIdCounter.current();
        _tokenIdCounter.increment();
        _validity[tokenId] = true;
        _safeMint(to, tokenId);
        _setTokenURI(tokenId, validURI);
        _setRedeemedTokenURI(tokenId, redeemedURI);
    }

    function _setRedeemedTokenURI(uint256 tokenId, string memory _tokenURI)
        internal
        virtual
    {
        require(
            _exists(tokenId),
            "ERC721URIStorage: URI set of nonexistent token"
        );
        _redeemedTokenURIs[tokenId] = _tokenURI;
    }
```

Once you made changes to the smart contract, don't forget to run `npx hardhat compile` again so the TypeScript system can correctly catch up with the new API defintion.

It is good practice to test this right away or even to write test before we add even the implementation (which we will do next). For now, we catch up with a test for new minting function in our `tests/TocketToken.ts` test file:

```
    it("Should mint a token with token ID 0 & 1 to account1", async function () {

      const { token, firstAccount } = await loadFixture(deployFixture);

      const address1=firstAccount.address;
     
      await token.safeMint(address1, validIPFS, redeemedIPFS );
      expect(await token.ownerOf(0)).to.equal(address1);

      await token.safeMint(address1, validIPFS, redeemedIPFS);
      expect(await token.ownerOf(1)).to.equal(address1);

      expect(await token.balanceOf(address1)).to.equal(2);      
    });

    it("Should mint a token with token ID 0 to contract owner, transfer to account2 and check the account2 balance", async function () {

      const { token, owner, secondAccount } = await loadFixture(deployFixture);

      const addressOwner=owner.address;
      await token.safeMint(addressOwner, validIPFS, redeemedIPFS);
      expect(await token.ownerOf(0)).to.equal(addressOwner);

      const address2 = secondAccount.address;
      const tokenId = 0;
      await token.transferFrom(addressOwner, address2, tokenId);
      expect(await token.ownerOf(0)).to.equal(address2);

      expect(await token.balanceOf(addressOwner)).to.equal(0);      
      expect(await token.balanceOf(address2)).to.equal(1);      
    });
```

For the new test to work, we need to add two constants for the valid/redeemed IPFS URLs. Add these just below the imports of the test to make them global, so we can refer to these from all other tests later on:

```
const validIPFS = "ipfs://bafkreiffperituayfv5thxqtn45mldwvz5vratkzheitoswxqzoh67ab4q"; //Inspire
const redeemedIPFS = "ipfs://bafybeiadeh5hy3uw2oajyfpudedzdfai7ffgwvaifm2hq7ps4iiimdy6tm/4.json"; //SAP Academy
```

 We also need a way to mark a TicketToken NFT used - for this we need to add another function. But let's now create a test for this first. Once a ticket has been redeemed using the redeem function, we need to check the state was correctly put onto the blockchain.

 We will create a new test section for all utility tests:

 ```
describe("Utility", function () {

    it("Should mint a token and check that validity is true", async function () {

      const { token, firstAccount } = await loadFixture(deployFixture);
  
      const address1 = firstAccount.address;
      const tokenId = 0;
      await token.safeMint(address1, validIPFS, redeemedIPFS);
      expect(await token.ownerOf(tokenId)).to.equal(address1);
      expect(await token.isAvailable(tokenId)).to.equal(true);      
    });

    it("Should mint a token, redeem and check that validity is false", async function () {

      const { token, firstAccount } = await loadFixture(deployFixture);
  
      const address1 = firstAccount.address;
      const tokenId = 0;
      await token.safeMint(address1, validIPFS, redeemedIPFS);
      await token.redeem(tokenId);
      expect(await token.ownerOf(tokenId)).to.equal(address1);
      expect(await token.isAvailable(tokenId)).to.equal(false);      
    });

  });
 ```

If you run `npx hardhat test` we expect to see a couple of issues, mainly our contract is missing a redeem and isAvailable function. Let's add these to the contract:

```
    function redeem(uint256 tokenId) public onlyOwner {
        _validity[tokenId] = false;
    }

    function isAvailable(uint256 tokenId) public view returns (bool) {
        return _validity[tokenId];
    }
```
(Don't forget to compile with `npx hardhat compile`...)

`npx hardhat test` should now happily walk through all the tests.

Let's now write another test that tests if only the owner of the contract, which could be the organizer of a concert, is able to mark a ticket as redeemed. 

```
    it("Should mint a token, thow exception as token owner tries to mark used (onlyOwner = contract owner)", async function () {

      const { token, firstAccount } = await loadFixture(deployFixture);
  
      const address1 = firstAccount.address;
      const tokenId = 0;
      await token.safeMint(address1, validIPFS, redeemedIPFS);
      await expect(token.connect(firstAccount).redeem(tokenId)).to.be.revertedWith(
        "Ownable: caller is not the owner"
      );
    });
```

This should just work and confirms that the Solidity modifier 'onlyOwner' works just as expected for our redeem function. 

Let's now work on the transferability features. If you recall, only a valid (not redeemed) ticket shall be transferable to another user. Once redeemed, no more transfers should be accepted. We will first write two tests for this:

```
    it("Should mint a token and check that validity is true and transfer token to address2", async function () {

      const { token, firstAccount, secondAccount } = await loadFixture(deployFixture);
  
      const address1 = firstAccount.address;
      const address2 = secondAccount.address;
      const tokenId = 0;
      await token.safeMint(address1, validIPFS, redeemedIPFS);
      await token.connect(firstAccount).transferFrom(address1, address2, tokenId);

      expect(await token.ownerOf(tokenId)).to.equal(address2);
      expect(await token.isAvailable(tokenId)).to.equal(true);
    });

    it("Should mint a token, mark used and verify not transferable", async function () {

      const { token, firstAccount, secondAccount } = await loadFixture(deployFixture);
  
      const address1 = firstAccount.address;
      const address2 = secondAccount.address;
      const tokenId = 0;
      await token.safeMint(address1, validIPFS, redeemedIPFS);
      await token.redeem(tokenId);      
      expect(await token.isAvailable(tokenId)).to.equal(false);


      await expect(token.connect(firstAccount).transferFrom(address1, address2, tokenId)).to.be.revertedWith(
         "TICKET: ticket has already been used."
       );
    });
```
Running the tests right now will fail the second test, as we have not yet implemented the non-transferability feature. Let's change the transfer method of our smart contract. Add the following which overrides the built-in transfer method and adds another check:

```
    function _transfer(
        address from,
        address to,
        uint256 tokenId
    ) internal override(ERC721) {
        require(
            (_validity[tokenId] == true),
            "TICKET: ticket has already been used."
        );
        super._transfer(from, to, tokenId);
    }
```

Now the smart contract will revert transactions that do not satisfy the require rule we have added. Running the tests again should now again pass all tests.

The final feature to implement is to change the appearance of our NFT based on the availability status. Let's again first add two tests:

```
it("Should mint a token and check that tokenURI is validIPFS", async function () {

  const { token, firstAccount } = await loadFixture(deployFixture);

  const address1 = firstAccount.address;
  const tokenId = 0;
  await token.safeMint(address1, validIPFS, redeemedIPFS);
  expect(await token.tokenURI(tokenId)).to.equal(validIPFS);
});

it("Should mint a token, redeem and check that tokenURI is redeemedIPFS", async function () {

  const { token, firstAccount } = await loadFixture(deployFixture);

  const address1 = firstAccount.address;
  const tokenId = 0;
  await token.safeMint(address1, validIPFS, redeemedIPFS);
  await token.redeem(tokenId);
  expect(await token.tokenURI(tokenId)).to.equal(redeemedIPFS);
});
```

For this feature, we need to change the tokenURI method. It is already available in the `TicketToken.sol` contract and needs to be changed to this implementation:

```
function tokenURI(uint256 tokenId)
    public
    view
    override(ERC721, ERC721URIStorage)
    returns (string memory)
{
    require(_exists(tokenId), "ERC721URIStorage: tokenId not set");

    if (_validity[tokenId] == true) {
        return super.tokenURI(tokenId);
    } else {
        return _redeemedTokenURIs[tokenId];
    }
}
```

As you can see, it's a simple `if statement` that helps us to distinguish the two URIs. Run the tests - they should all pass.  

Congratulations! By now you have added a lot of functionality to our NFT smart contract and you have even verified most functionality with tests. If you want, feel free to deploy the new smart contract to Goerli (check section 2 of this tutorial) and test it all out on a real testnet. 

Thx for staying with us this far. If you have extra questions, we're here to help. Just let us know!



## How to obtain support

Support for the content in this repository is available during the actual time of the online session for which this content has been designed. Otherwise, you may request support via the [Issues](../../issues) tab.

## License
Copyright (c) 2022 SAP SE or an SAP affiliate company. All rights reserved. This project is licensed under the Apache Software License, version 2.0 except as noted otherwise in the [LICENSE](LICENSES/Apache-2.0.txt) file.
