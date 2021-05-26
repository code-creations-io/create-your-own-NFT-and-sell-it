# create-your-own-NFT-and-sell-it

## Prerequisities

Make sure you have the following installed:

- Python
- Node JS & npm
- MetaMask
- A Facebook or Twitter account

`MetaMask` is wallet that runs as a self-contained application inside of your browser as an extension. It allows you to interact with Decentralised Applications. MetaMask makes accessing the Ethereum and Testnet blockchains very easy and provides you with wallet addresses. To install it:

- Go to https://metamask.io and install the browser extension in Chrome or Firefox
- Once downloaded it should redirect you to an onboarding screen, but if not click on the MetaMask icon in your extensions
- Create a new wallet
- During setup you will be presented with a secret phrase, copy and keep it somewhere secure where nobody else can access it
- Once you've created your wallet you'll see that you are based on the Ethereum Mainnet network. Change this so that you're on the `Rinkeby Test Network`

To complete this tutorial we will need some `Rinkeby ETH` and `Rinkeby LINK`, which are Ethereum and Chainlink respectively. We will use the ETH to use the deploy our smart contracts on the Ethereum blockchain and to get data from off-chain we will use our Chainlink. Chainlink is what's called a `Blockchain oracle`, in short it is the middleware berween the real world and the blockchain world https://dev.to/patrickalphac/off-chain-data-in-ethereum-how-to-access-4395. Let's start by getting some free Rinkeby ETH:

- Go back to your MetaMask account and copy your Account ID. If you hover above your account name it should open a prompt for you to copy it
- Go to https://faucet.rinkeby.io/
- Go to your Twitter or Facebook account, past your MetaMask Account ID into a new post and create your post. Follow the guidelines at the bottom of https://faucet.rinkeby.io/
- Copy the URL of this post on Twitter or Facebook
- Paste it into the input bar on https://faucet.rinkeby.io/
- Wait for the transaction to complete and you should now have some ETH in your MetaMask account!

Now we want to add some Rinkeby LINK to our wallet, we do this by:

- Go to https://rinkeby.chain.link/
- Paste your MetaMask Account ID into the input box and click on the button to send 100 Test LINK
- When complete, a green notification will appear at the top of screen with a link for you to view this transaction on the blockchain. Click on this link to view the transaction
- Wait for this transaction to be confirmed by a miner on the blockchain, when it has been confirmed the status of this transaction should change to `Success`
- If you check your MetaMask wallet, nothing will have changed. You now need to go back to your MetaMask account and click on `Add Token`
- Complete the form on this page to add your Rinkeby LINK
- The `Token Contract Address` can be found by searching for `LINK Rinkeby address` or by going to https://docs.chain.link/docs/acquire-link/ and looking for the `Rinkeby LINK Token`
- After pasting the `Token Contract Address`, click next and confirm the Chainlink token
- Now we're ready to go with our Ethereum and Chainlink!

---

## Setup environment

Now we need to install two things `ganache-cli` and `eth-brownie`. You can do this with these two commands:

    npm install -g ganache-cli
    pip install eth-brownie

For the code, let's make use of some excellent boilerplate code provided an NFT Brownie Mix repo. Use this command to clone all of the files into your current directory:

    git clone https://github.com/PatrickAlphaC/nft-mix .

Finally we need to create some environment variables, go to the `.env` file in your directory and update the following variables

```
export PRIVATE_KEY=<enter private key>
export WEB3_INFURA_PROJECT_ID=<enter Infura Project ID>
```

- The private key can be found by accessing your MetaMask account, clicking on the 3 dots to bring up your account details and finally by clicking `Export Private Key`
- The WEB3_INFURA_PROJECT_ID can be accessed by creating a free Infura account. This account will give us a way to send transactions to the blockchain.
- To create an Infura account, to to https://infura.io/
- Sign up with an email and password
- Confirm email address and then create an Ethereum project
- Once created, you'll be presented with it's `PROJECT_ID`. Use this to create the `WEB3_INFURA_PROJECT_ID` within the `.env` file
- Make sure to also change the Endpoint of the Ethereum project to `Rinkeby`

As for the other environment variables in this file, keep them commented for now.

---

## Create our first contract and deploy!

Now we're ready to run our code to deploy our NFT contract to the Rinkeby blockchain and create our first collectible! We will be deploying it on a platform called `OpenSea`. To do this, source the environment variables by running `source .env` within the root of your project. Now let's create a simple NFT contract on the Rinkeby blockchain by running:

    brownie run scripts/simple_collectible/deploy_simple.py --network rinkeby

And then let's create our first collectible by running:

    brownie run scripts/simple_collectible/create_collectible.py --network rinkeby

---

## Diving into the simple contract

To take a look at the contract that we just deployed, open up the file at this location `contracts/SimpleCollectible.sol`, which is a `solidity` file. It should look something like this:

```
// SPDX-License-Identifier: MIT
pragma solidity 0.6.6;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";

contract SimpleCollectible is ERC721 {
    uint256 public tokenCounter;
    constructor () public ERC721 ("Dogie", "DOG"){
        tokenCounter = 0;
    }

    function createCollectible(string memory tokenURI) public returns (uint256) {
        uint256 newItemId = tokenCounter;
        _safeMint(msg.sender, newItemId);
        _setTokenURI(newItemId, tokenURI);
        tokenCounter = tokenCounter + 1;
        return newItemId;
    }

}
```

You can see that first we are importing a package for our ERC721 token from OpenZeppelin. ERC721 is a token standard for non-fungible tokens which states that even though someone may be able to make a copy of this token, there will always be only one of this token. On the other hand, fungible tokens follow a different standard such as ERC20s making them replaceable or interchangeable.

The ERC721 package gives us all the functionality that we will need for our tokens and we inherit all of these when we instantiate the `SimpleCollectible`. Within the constructor we give our NFT a name and a symbol, in this case `Dogie` and `DOG`. This means that every NFT we create using this contract will be of type `Dogie/DOG`, just like how every Pokemon on a Pokemon card is still a Pokemon, each Pokemon is unique but they are all Pokemon. In this case, we are just using a `DOG` as an example.

Also within the constructor we have a `tokenCounter` which counts how many NFT's we've created of this type. This token counter is then used to create a `token_id` within our Python script.

After the constructor, we have our function that created the collectible. This is what gets called from our `create_collectible.py` script. The `safeMint` function creates the new NFT and assigns it to whoever called the `createCollectible`, in this case `msg.sender`, along with a `newItemId` which based on the `tokenCounter`. This is how you can keep track of who owns what, by checking the owner of the `tokenId`.

Finally, we called `setTokenURI`, the `tokenURI` for an NFT is a unique identifier of what the token looks like. The URI could be an API call over HTTPS, an IPFS hash or anything else unique. If you look in our `create_collectible.py` script, we define the `sample_token_uri` as a HTTPS resource. If you open this up in a web browser, you'll see this:

```
{
    "name": "PUG",
    "description": "An adorable PUG pup!",
    "image": "https://ipfs.io/ipfs/QmSsYRx3LpDAb1GZQm7zZ1AuHZjfbPkD6J7s9r41xu1mf8?filename=pug.png",
    "attributes": [
        {
            "trait_type": "cuteness",
            "value": 100
        }
    ]
}
```

This is all of the metadata that was used to build our collectible! It tells us what the NFT looks, its image, its attributes, its name and a description. This metadata follows a particular standard, reflected by the above JSON, which makes it easy for platforms such as OpenSea, Rarible, Mintable etc... to render NFT's because they all follow this standard!

## On-chain vs Off-chain metadata

When smart contracts and NFT's came about it was super expensive to deploy a lot of data to the blockchain. Images as small as 1KB can easily cost over $1M to store! https://ethereum.stackexchange.com/questions/872/what-is-the-cost-to-store-1kb-10kb-100kb-worth-of-data-into-the-ethereum-block/896#896. This was a huge issue, especially when wanting to store creative or digital arts! To get around this, data is stored off-chain and on-chain. We store metadata on-chain so that we can program NFT's to interact with each other and we store some parts of the data off-chain, such as the image, because there is still not a great way to store large images on-chain. This is where `Chainlink` comes in, as mentioned earlier this is essentially the middleware that connects the blockchain world to the physical world.

Remember that a blockchain, a distributed ledger, works by each node in the network being able to find the same end result given the same input, i.e. validate each block. When transactions are very simple, such as Bob and Alice exchanging £5, this can be easily validated and reproduced by every node in the network.

But in the real world transactions aren't that simple, for example what if each transaction on a blockchain was validated by API's and Bob sends Alice a variable amount of £ based on the price of ETH. When every node on the network attempts to validate this transaction, they may get a different result because the API call that they made may return a different value of £, even if it was a fraction of a second later. Therefore none of the nodes would be able to agree and the system would fail.

Therefore blockchain are designed to be entirely deterministic, that is if we were to replay every transaction, we would end up in the same end state. So if you start to introduce non-deterministic sources into a blockchain, such as API's which may change, be hacked or even deprecated, we can't validate the transactions.

### So how does a blockchain oracle solve this problem?

A blockchain oracle is any device or entity that connects a deterministic blockchain with off-chain data. They enter every data input through an external transaction. This way we can be sure that the blockchain contains all of the information required to verify itself.

However, one of the main reasons why we use a blockchain in the first place is for this idea of a decentralised network, with no single point of failure. But if we start storing data externally in a centralised fashion, this bring us back to square one right?

`Chainlink` solves this problem and is the standard for decentralised oracles. A decentralised oracle or decentralised oracle network is a group of independent blockchain oracles that provide data to a blockchain. Every independent node or oracle in the decentralised oracle network independently retrieves data from an off-chain source and brings it on-chain. This data is then aggregated so the system can come to a deterministic value of truth for that data point.

We these oracles, we are leveraging the same reliable decentralised infrastructure behind blockchain, but for blockchain oracles in order to connect real world data to the blockchain and to enable smart contracts. If nodes on the oracle network are hacked, Chainlink will leverage the decentralised network to carry on.

---

## Creating an advanced dynamic NFT

A dynamic NFT is one that can change over time or have on-chain features that we can use to interact with each other. Use cases for these include unlimited customisation for video games, game characters or interactive art.

To get things started with our advanced NFT, run the following two commands:

    brownie run scripts/advanced_collectible/deploy_advanced.py --network rinkeby
    brownie run scripts/advanced_collectible/create_collectible.py --network rinkeby

Out collectible here is a random dog breed from `Chainlink VRF`, whuch provides random numbers specifically for smart contracts. The key here is that these random numbers are provably-fair and have a verifiable source of randomness which is ideal for smart contracts that rely on a unpredictable outcomes, e.g. in video games.

Next, we want to create it's metadata by running:

    brownie run scripts/advanced_collectible/create_metadata.py --network rinkeby

Then we need to set the token URI by running:

    brownie run scripts/advanced_collectible/set_tokenuri.py --network rinkeby

And there we have it! Our advanced collectible should now be deploying to OpenSea!

But what exactly did we do here, well let's look at the `AdvancedCollectible.sol` code and take a look:

```
pragma solidity 0.6.6;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@chainlink/contracts/src/v0.6/VRFConsumerBase.sol";

contract AdvancedCollectible is ERC721, VRFConsumerBase {
    uint256 public tokenCounter;
    enum Breed{PUG, SHIBA_INU, ST_BERNARD}
    // add other things
    mapping(bytes32 => address) public requestIdToSender;
    mapping(bytes32 => string) public requestIdToTokenURI;
    mapping(uint256 => Breed) public tokenIdToBreed;
    mapping(bytes32 => uint256) public requestIdToTokenId;
    event requestedCollectible(bytes32 indexed requestId);


    bytes32 internal keyHash;
    uint256 internal fee;

    constructor(address _VRFCoordinator, address _LinkToken, bytes32 _keyhash)
    public
    VRFConsumerBase(_VRFCoordinator, _LinkToken)
    ERC721("Dogie", "DOG")
    {
        tokenCounter = 0;
        keyHash = _keyhash;
        fee = 0.1 * 10 ** 18;
    }

    function createCollectible(string memory tokenURI, uint256 userProvidedSeed)
        public returns (bytes32){
            bytes32 requestId = requestRandomness(keyHash, fee, userProvidedSeed);
            requestIdToSender[requestId] = msg.sender;
            requestIdToTokenURI[requestId] = tokenURI;
            emit requestedCollectible(requestId);
    }

    function fulfillRandomness(bytes32 requestId, uint256 randomNumber) internal override {
        address dogOwner = requestIdToSender[requestId];
        string memory tokenURI = requestIdToTokenURI[requestId];
        uint256 newItemId = tokenCounter;
        _safeMint(dogOwner, newItemId);
        _setTokenURI(newItemId, tokenURI);
        Breed breed = Breed(randomNumber % 3);
        tokenIdToBreed[newItemId] = breed;
        requestIdToTokenId[requestId] = newItemId;
        tokenCounter = tokenCounter + 1;
    }

    function setTokenURI(uint256 tokenId, string memory _tokenURI) public {
        require(
            _isApprovedOrOwner(_msgSender(), tokenId),
            "ERC721: transfer caller is not owner nor approved"
        );
        _setTokenURI(tokenId, _tokenURI);
    }
}
```

As said before, we used `Chainlink VRF` to select a random breed of dog from PUG, SHIBA_INU or BERNARD using `requestRandomness`. The Chainlink node responds by calling the `fulfillRandomness` function and creates the collectible. Finally, we call our `setTokenURI` to give our NFT it's appearance.

If you take a look inside `scripts/advanced_collectible/create_metadata.py` you can see that opur token URI is already defined for us for each breed of dog:

```
breed_to_image_uri = {
    "PUG": "https://ipfs.io/ipfs/QmSsYRx3LpDAb1GZQm7zZ1AuHZjfbPkD6J7s9r41xu1mf8?filename=pug.png",
    "SHIBA_INU": "https://ipfs.io/ipfs/QmYx6GsYAKnNzZ9A6NvEKV9nf1VaDzJrqDR23Y8YSkebLU?filename=shiba-inu.png",
    "ST_BERNARD": "https://ipfs.io/ipfs/QmUPjADFGEKmfohdTaNcWhp7VGk26h5jXDA7v3VtTnTLcW?filename=st-bernard.png",
}
```

In this advanced example we didn't really get much control over the image we use or the token URI file containing our metadata. However, if we wanted to do this ourselves for complete customisation, we can do this too. We can do this by using `IPFS`, which is a free decentralised platform that will allow us to store:

- The image of the NFT
- And the token URI file

To do this, we need to do a few things:

- Download IPFS desktop: https://github.com/ipfs/ipfs-desktop
- Click on the `Files` tab
- Click on the `Import` button and choose file
- Choose a file on your local machine
- Wait for it to upload and then click on the elipsis (three dots) for this file
- Click `Share link`
- Copy the link
- And finally replace the link(s) within `scripts/advanced_collectible/set_tokenuri.py` with our new token URI!

There is one other thing to mention, if the tokenURI is only on our node, this means that when our node is down no one else can view it. To get over this issue, we want others to pin our NFT and we can use a service such as `Pinata` https://pinata.cloud/ to keep our data alive even when our IPFS node is down. Another option would be to use something like Filecoin https://docs.filecoin.io/ since Pinata is not as decentralised as it should be.
