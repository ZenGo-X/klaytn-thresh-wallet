# Klaytn Threshold Wallet

Klaytn wallet and JS SDK powered by two-party ECDSA.

Requirements
=================
The following packages are required to use the library:
- [Rust](https://rustup.rs/). Recommended version: 1.36.0-nightly (d35181ad8 2019-05-20).
- [Node.js](https://nodejs.org/en/download/)
- [npm](https://www.npmjs.com/get-npm)
- [gcc-c++](https://gcc.gnu.org/)

Testing in klaytn-thresh-wallet is implemented using the mocha testing framework. If you want to run unit tests in klaytn-thresh-wallet, you need to install mocha first.
- [mocha](https://mochajs.org/#installation)

**Note** klaytn-thresh-wallet can run on Node.js versions 8 and 10, and the recommended versions are:
- lts/carbon ([8.16.0](https://nodejs.org/dist/latest-v8.x/))
- lts/dubnium ([10.16.0](https://nodejs.org/dist/latest-v10.x/))

If you are already using a different version of the node(for example, node v12), use the Node Version Manager([NVM](https://github.com/nvm-sh/nvm)) to install and use the version supported by klaytn-thresh-wallet.

Usage
=================
```
$ git clone https://github.com/KZen-networks/klaytn-thresh-wallet.git
$ cd klaytn-thresh-wallet
$ yarn install
$ yarn run build
```
Built files will be located in the `dist` folder.

Launch server (acts as the co-signer in the two-party signing protocol):
```js
const { Party1 } = require('../index');
const p1 = new Party1();
p1.launchServer();
```
Client:
```js
const Caver = require('../index');
const caver = new Caver('https://api.baobab.klaytn.net:8651/');
const caverUtils = caver.utils;

(async function () {
    const {share, address} = await caver.klay.accounts.createFromTwoParty();
    console.log(address);
    // 0x477d627398FE73f4610c6EdC24218b78671E7991

    /* Now deposit some Klay to the address created above */

    caver.klay.accounts.wallet.addWithPrivateShare(
        share,
        address
    );
    const signedTx = await caver.klay.accounts.signTransactionWithShare({
        type: 'VALUE_TRANSFER',
        from: address,
        to: address,  // or whatever destination address
        gas: '300000',
        value: caverUtils.toPeb(klay_amount)
    }, share);

    const result = await caver.klay.sendSignedTransaction(signedTx.rawTransaction);
    console.log(result);
})();
```

Demo
=================
You can also use the **CLI demo** to test the functionality of two-party signing scheme.<br>
Server:
```sh
$ demo/server
🔧 Configured for development.
    => address: localhost
    => port: 8000
    => log: critical
    => workers: 24
    => secret key: private-cookies disabled
    => limits: forms = 32KiB
    => keep-alive: 5s
    => tls: disabled
🚀 Rocket has launched from http://localhost:8000
```
Client:
```sh
$ demo/client --help
Usage: client [options] [command]

Options:
  -h, --help                                                    output usage information

Commands:
  address
  balance <address>
  token_balance <address> <token_contract_address>
  token_listen <to> <token_contract_address>
  token_transfer <from> <to> <amount> <token_contract_address>
  transfer <from> <to> <klay_amount>
```
|![Transfer demo](https://github.com/KZen-networks/klaytn-thresh-wallet/blob/feature/two-party-ecdsa/demo/Klaytn%20TSS-2.gif?raw=true "Klaytn Threshold Wallet Demo")|
|:--:|

## License
MIT

## Credits
This work is a fork extending Klaytn's [caver-js](https://github.com/klaytn/caver-js).
For further documentation is advised to check out that repository.
