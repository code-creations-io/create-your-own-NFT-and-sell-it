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

To complete this tutorial we will need some `Rinkeby ETH` and `Rinkeby LINK`, which are Ethereum and Chainlink respectively. We will use the ETH to use the deploy our smart contracts on the Ethereum blockchain and to get data from off-chain we will use our Chainlink. Chainlink is what's called a `Blockchain oracle`, in short it is the middleware berween the real world and the blockchain world. Let's start by getting some free Rinkeby ETH:

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
