# Multi-Blockchain Wallet in Python

![newtons-coin-cradle](Images/newtons-coin-cradle.jpg)

## Universal Blockchain Wallet

A Blockchain wallet is a digital wallet that allows users to store and manage their crypto currencies. Each of the Crypto currencies have 
designed their own wallets the users can use to manage their keys and account balances. but having multiple wallets for each currency is not a practical approach. 

Thankfully, There's a command line tool, `hd-wallet-derive` that supports not only BIP32, BIP39, and BIP44, standards but also supports non-standard derivation paths for the most popular wallets out there today! However, we need to integrate the script into our blockchain with Python. Once we've integrated this "universal" wallet, we can begin to manage billions of addresses across 300+ coins.

In this project, however, we will try to create a wallet to get 2 coins working: Ethereum and Bitcoin Testnet.
Ethereum keys are the same format on any network, so the Ethereum keys should work with your custom networks or testnets. Hence we will use of local
testnet Ethereum network we developed using PoA consensus algorithm. ['PoA BlockChain'](https://github.com/pdevalaraju/POA_Development_Blockchain). 
This will demonstrate our wallets ability to not only intereact with publicly available testnet blockchains but be able to interact and make transactions within our local blockchain network using the same wallet. 

## Dependencies

- PHP must be installed on your operating system (any version, 5 or 7). 

- Clone the [`hd-wallet-derive`](https://github.com/dan-da/hd-wallet-derive) tool.

- intall [`bit`](https://ofek.github.io/bit/) Python Bitcoin library.

- install [`web3.py`](https://github.com/ethereum/web3.py) Python Ethereum library.

## Installation Instructions for hd-wallet-derive

- Create a project directory called `wallet` and `cd` into it.

- download [`hd-wallet-derive`](https://github.com/dan-da/hd-wallet-derive) tool and Clone the `hd-wallet-derive` tool into wallet folder and extract the contents of it into the same folder. 

- Create a symlink called `derive` for the `hd-wallet-derive/hd-wallet-derive.php` script into the top level project
  directory like so: `ln -s hd-wallet-derive/hd-wallet-derive.php derive`

- This will clean up the command needed to run the script in our code, as we can call `./derive`
  instead of `./hd-wallet-derive/hd-wallet-derive.php`.

- Test that you can run the `./derive` script properly, use one of the examples on the repo's `README.md`

The directory structure should look something like this:

![directory-tree](Images/tree.png)

### Setup constants

- In a separate file, `constants.py`, set the following constants: This is used to configue the crypto currencies we want to manage using our wallet. This file will be imported into our wallet.py to make the configured token/coin types accessible via our wallet program via referencing these strings, both in function calls, and in setting object keys.

  - `BTC = 'btc'`
  - `ETH = 'eth'`
  - `BTCTEST = 'btc-test'`

### Generate a Mnemonic

- Generate a new 12 word mnemonic using `hd-wallet-derive` or by using [this tool](https://iancoleman.io/bip39/).

- Set this mnemonic as an environment variable, and save it in .ENV file which can be called via the wallet everytime you want to access the wallet keys: `mnemonic = os.getenv('MNEMONIC', 'insert mnemonic here')`

### Deriving the wallet keys

- We will make use of the `subprocess` library to call the `./derive` script from Python. 

- The following flags are configured/passed into the shell command as variables:
  - Mnemonic (`--mnemonic`) must be set from an environment variable, or default to a test mnemonic
  - Coin (`--coin`)   - reads from the constants.py program we imported. 
  - Numderive (`--numderive`) to set number of child keys generated

- With the `--format=json` flag, we then parse the output into a JSON object using `json.loads(output)` and return the dictionary object of keys & addresses for each of the crypto currencies. The object is assigned to a dictionary variable called coins, when called will show the output as below.

![wallet-object](Images/wallet_object.PNG)

We can select child accounts (and thus, private keys) by calling `coins[COINTYPE][INDEX]['privkey']` where the token is either BTCTEST or ETH, index is the index # of the key for each token and the 'privkey' as the reference to the dictionary item. 

![wallet-keys](Images/wallet_keys.PNG)

- Wewill wrap all of the above into one function, called `derive_wallets` which uses the parameters/flags configured above and fetches the keys from 
[this tool](https://iancoleman.io/bip39/) using your Mnemonic phrase. 


### Linking the transaction signing libraries

We will use `bit` and `web3.py` to leverage the keys we've got in the `coins` object using three more functions:

- Function `priv_key_to_account` -- this will convert the `privkey` string in a child key to an account object that `bit` or `web3.py` can use to transact. This function accepts the following parameters: 

  - `coin` -- the coin type (defined in `constants.py`).
  - `priv_key` -- the `privkey` string will be passed through here.

  Depending on the coin/token we seleccted, this function returns one of the following based on the library:

  - For `ETH`, returns `Account.privateKeyToAccount(priv_key)`
      - Account.privateKeyToAccount function is part of web3 librarary which returns an account object from the private key string. 	(https://web3js.readthedocs.io/en/v1.2.0/web3-eth-accounts.html#privatekeytoaccount).
  - For `BTCTEST`, return `PrivateKeyTestnet(priv_key)`
      - PrivateKeyTestnet is part of bit.py libarary that converts the private key string into a WIF (Wallet Import Format) object. WIF is a special 	format bitcoin uses to designate the types of keys it generates. [here](https://ofek.dev/bit/dev/api.html).

- Function `create_tx` -- this function will create the raw, unsigned transaction that contains all metadata needed to transact. The following parameters are to be passed to this function 
  - `coin` -- the coin type (defined in `constants.py`).
  - `account` -- the account object from `priv_key_to_account`.
  - `to` -- the recipient address.
  - `amount` -- the amount of the coin to send.

  Depending on the type of coin selected, this function returns one of the following functions based on the library:

  - For `ETH`, return an object containing `to`, `from`, `value`, `gas`, `gasPrice`, `nonce`, and `chainID`.
  - For `BTCTEST`, return `PrivateKeyTestnet.prepare_transaction(account.address, [(to, amount, BTC)])`

- Function `send_tx` -- this will call `create_tx`, sign the transaction, then send it to the designated network. This function accepts the following parameters:

  - `coin` -- the coin type (defined in `constants.py`).
  - `account` -- the account object from `priv_key_to_account`.
  - `to` -- the recipient address.
  - `amount` -- the amount of the coin to send.

  Depending on the type of coin selected, the function creates a `raw_tx` object by calling `create_tx` and then signs the raw transaction using 
  using `bit` or `web3.py' and broadcasts to the respective blockchain networks.

  - For `ETH`, return `w3.eth.sendRawTransaction(signed.rawTransaction)`
  - For `BTCTEST`, return `NetworkAPI.broadcast_tx_testnet(signed)`

Now, we should be able to fund these wallets using testnet faucets. 

#### Bitcoin Testnet transaction

- Fund a `BTCTEST` address using [this testnet faucet](https://testnet-faucet.mempool.co/).

- Use a [block explorer](https://tbtc.bitaps.com/) to watch transactions on the address.

- Send a transaction to another testnet address (either one of your own, or return back a part of the fund recieved to the sender).

- Below is the Screenshot of transaction confirmation:

![btc-test](Images/btc_tx.PNG)

#### Local PoA Ethereum transaction using ['POATestnet'](https://github.com/pdevalaraju/POA_Development_Blockchain)

- Add one of the `ETH` addresses to the pre-allocated accounts in your `poatestnet.json`.

- Initialize using `geth --datadir nodeX init poatestneet.json`. This will run our preconfigured local blockchain, and will pre-fund the new account.

- Send a transaction from the pre-funded address within the wallet to another, then copy the `txid` into
  MyCrypto's TX Status. Below is the screenshot of successful transaction :

![eth-test](Images/eth_tx.PNG)

### Submission

- Create a `README.md` that contains the test transaction screenshots, as well as the code used to send them.
  Pair the screenshot with the line(s) of code.

- Write a short description about what the wallet does, what is is built with, and how to use it.

- Include installing pip dependencies using `requirements.txt`, as well as cloning and installing `hd-wallet-derive`.
  You may include the `hd-wallet-derive` folder in your repo, but still include the install instructions. You do not
  need to include Python or PHP installation instructions.

- Add a function to track transaction status by `txid`.
