# Excercise 3 - Adding utility to your smart contracts

In this exercise we will go beyond the standard functionality of NFTs. This is what we think is really interesting, especially when it comes to potential integration with other systems at SAP. While we discuss utilities in our workshop talk track and presentation, for this exercise we need to make sure it's achievable in short time. That's why we will transform our existing standard NFT into a TicketToken: the NFT is mean to be used as an event ticket and can redeemed, transferred to another user, etc. 

## Preparing the TicketToken.sol smart contract

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
- once a ticket has been redeemed, it cannot be transferred any more. This could be a great feature which prevents the sale of already redeemed tickets on marketplaces such as OpenSea. 
- the appearance of our TicketToken NFT should change based on the status of the validity. A valid, non-redeemed ticket might have the look of a typical paper ticket. Once redeemed, the nft image should change into another picture, for example to remember the great event. 

## Storing additional information: ticket (NFT) validity

To store the validity of each NFT, we need to introduce a new mapping inside our smart contract. Put the following lines just above the constructor of the `TicketToken.sol` smart contract:

```
//mapping from tokenID to bool (validity true/false)
mapping(uint256 => bool) private _validity;
```

And we also want to be able to change the appearance of our NFT based on the validity state, so we need to add another mapping for the second URI that we need to store. Add this also anywhere above the constructor:

```
//mapping for tokenURIs for redeemed tickets
mapping(uint256 => string) private _redeemedTokenURIs;
```

Before we add the functionality to the functions of the NFT, let's now start to create a new test in the `tests` folder. Therefore, create the file `TicketToken.ts` inside the `test` folder. Below is a template wiht an initial test for the token name and symbol that you can use:

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

## Augmenting a standard function - storing more than one token URI

As the token minting function - safeMint - will now have to take two token URIs, one for the valid version and one for the redeemed version, we have to **replace** the safeMint function. To accomodate for the storage of the second URI, we simply create another function - `_setRedeemedTokenURI`. Add the following code to the `contracts/TicketToken.sol` file (replace safeMint if it is already there).

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

For the new test to work, we need to add two constants for the valid/redeemed IPFS URLs. Add these **just below the imports** of the test to make them global, so we can refer to these from all other tests later on:

```
const validIPFS = "ipfs://bafkreigbny3owmnda6ojabcpyoukswh75tto4gpqkitswtv6tb4ypb7aaa";
const redeemedIPFS = "ipfs://bafkreiccyxvqbivjcjbebiuva4d2lwrg47bxmffjpho2cab3ktyacxqqbu";
```

Now we can run the test again:

```
npx hardhat test
```

## Storing token redemption on the blockchain

 We also need a way to mark a TicketToken NFT used - for this we need to add another function. But let's now create a test for this first. Once a ticket has been redeemed using the redeem function, we need to check the state was correctly put onto the blockchain.

 We will create a new test section for all utility tests. Therefore, we add the following `describe` code before the last line:

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

## Stopping tickets from being transferred after redemption

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

## Changing NFT metadata based on redemption status

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

Note: if you decide to deploy this smart contract later on and create a sample NFT, you might notice that marketplaces such as OpenSea still show the original NFT metadata even after redemption. That's because these marketplaces aggressively cache the IPFS metadata of the NFT. You can either wait a few hours until they refresh the cache or hit the "reload metadata" button that you find on OpenSea NFT detail pages.


## Summary

Congratulations! By now you have added a lot of functionality to our NFT smart contract and you have even verified most functionality with tests. If you want, feel free to deploy the new smart contract to Goerli (check [exercise 2](../ex2/README.md) of this tutorial) and test it all out on a real testnet. 

Thx for staying with us this far. If you have extra questions, we're here to help. Just let us know!