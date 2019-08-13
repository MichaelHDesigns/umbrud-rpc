# umbrud-rpc

[![Build Status](https://img.shields.io/travis/umbru/umbrud-rpc.svg?branch=master)](https://travis-ci.org/umbru/umbrud-rpc)
[![NPM Package](https://img.shields.io/npm/v/umbrud-rpc.svg)](https://www.npmjs.org/package/umbrud-rpc)

> Umbru Client Library to connect to Umbru Core (umbrud) via RPC

## Install

umbrud-rpc runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install umbrud-rpc
```

## Usage

### RpcClient

Config parameters : 

	- protocol : (string - optional) - (default: 'https') - Set the protocol to be used. Either `http` or `https`.
	- user : (string - optional) - (default: 'user') - Set the user credential.
	- pass : (string - optional) - (default: 'pass') - Set the password credential.
	- host : (string - optional) - (default: '127.0.0.1') - The host you want to connect with.
	- port : (integer - optional) - (default: 12354) - Set the port on which perform the RPC command.

Promise vs callback based

  - `require('umbrud-rpc/promise')` to have promises returned
  - `require('umbrud-rpc')` to have callback functions returned
	
### Examples

Config:

```javascript
var config = {
    protocol: 'http',
    user: 'umbru',
    pass: 'local321',
    host: '127.0.0.1',
    port: 12354
};
```

Promise based:

```javascript
var RpcClient = require('umbrud-rpc/promise');
var rpc = new RpcClient(config);

rpc.getRawMemPool()
    .then(ret => {
        return Promise.all(ret.result.map(r => rpc.getRawTransaction(r)))
    })
    .then(rawTxs => {
        rawTxs.forEach(rawTx => {
            console.log(`RawTX: ${rawTx.result}`);
        })
    })
    .catch(err => {
        console.log(err)
    })
```

Callback based (legacy):

```javascript
var run = function() {
  var bitcore = require('umbrucore-lib');
  var RpcClient = require('umbrud-rpc');
  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new bitcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

### Help

You can dynamically access to the help of each method by doing

```
const RpcClient = require('umbrud-rpc');
var client = new RPCclient({
    protocol:'http',
    user: 'umbru',
    pass: 'local321', 
    host: '127.0.0.1', 
    port: 12354
});

var cb = function (err, data) {
    console.log(data)
};

// Get full help
client.help(cb);

// Get help of specific method
client.help('getinfo',cb);
```

## Contributing

Feel free to dive in! [Open an issue](https://github.com/umbru/umbru-std-template/issues/new) or submit PRs.

## License

[MIT](LICENSE) &copy; Umbru Core Group, Inc.
