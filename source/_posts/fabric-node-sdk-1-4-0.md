---
title: fabric node sdk 1.4.0
date: 2020-04-07 13:49:57
tags:
---

## 初始化项目

```shell
npm init
```



## 安装依赖

 ```shell
npm install  --save fabric-ca-client
npm install  --save fabric-client
npm install --save grpc
 ```

## 编写代码

1) enrollAdmin.js

```javascript
'use strict';


// 注册管理员用户


var Fabric_Client = require('fabric-client');
var Fabric_CA_Client = require('fabric-ca-client');

var path = require('path');
var util = require('util');
var os = require('os');


var fabric_client = new Fabric_Client();
var fabric_ca_client = null;
var admin_user = null;
var member_user = null;
var store_path = path.join(__dirname, 'hfc-key-store');
console.log(' Store path:'+store_path);


Fabric_Client.newDefaultKeyValueStore({ path: store_path
}).then((state_store) => {

    fabric_client.setStateStore(state_store);
    var crypto_suite = Fabric_Client.newCryptoSuite();


    var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
    crypto_suite.setCryptoKeyStore(crypto_store);
    fabric_client.setCryptoSuite(crypto_suite);
    var tlsOptions = {
        trustedRoots: [],
        verify: false
    };

    fabric_ca_client = new Fabric_CA_Client('http://39.106.189.16:7054', tlsOptions , 'ca.example.com', crypto_suite);


    return fabric_client.getUserContext('admin', true);
}).then((user_from_store) => {
    if (user_from_store && user_from_store.isEnrolled()) {
        console.log('Successfully loaded admin from persistence');
        admin_user = user_from_store;
        return null;
    } else {

        return fabric_ca_client.enroll({
            enrollmentID: 'admin',
            enrollmentSecret: 'adminpw'
        }).then((enrollment) => {
            console.log('Successfully enrolled admin user "admin"');
            return fabric_client.createUser(
                {username: 'admin',
                    mspid: 'Org1MSP',
                    cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
                });
        }).then((user) => {
            admin_user = user;
            return fabric_client.setUserContext(admin_user);
        }).catch((err) => {
            console.error('Failed to enroll and persist admin. Error: ' + err.stack ? err.stack : err);
            throw new Error('Failed to enroll admin');
        });
    }
}).then(() => {
    console.log('Assigned the admin user to the fabric client ::' + admin_user.toString());
}).catch((err) => {
    console.error('Failed to enroll admin: ' + err);
});
```

2) registerUser.js

```shell
//注册普通用户

var Fabric_Client = require('fabric-client');
var Fabric_CA_Client = require('fabric-ca-client');

var path = require('path');
var util = require('util');
var os = require('os');


var fabric_client = new Fabric_Client();
var fabric_ca_client = null;
var admin_user = null;
var member_user = null;
var store_path = path.join(__dirname, 'hfc-key-store');
console.log(' Store path:'+store_path);


Fabric_Client.newDefaultKeyValueStore({ path: store_path
}).then((state_store) => {

    fabric_client.setStateStore(state_store);
    var crypto_suite = Fabric_Client.newCryptoSuite();


    var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
    crypto_suite.setCryptoKeyStore(crypto_store);
    fabric_client.setCryptoSuite(crypto_suite);
    var tlsOptions = {
        trustedRoots: [],
        verify: false
    };

    fabric_ca_client = new Fabric_CA_Client('http://localhost:7054', null , '', crypto_suite);


    return fabric_client.getUserContext('admin', true);
}).then((user_from_store) => {
    if (user_from_store && user_from_store.isEnrolled()) {
        console.log('Successfully loaded admin from persistence');
        admin_user = user_from_store;
    } else {
        throw new Error('Failed to get admin.... run enrollAdmin.js');
    }



    return fabric_ca_client.register({enrollmentID: 'user1', affiliation: 'org1.department1',role: 'client'}, admin_user);
}).then((secret) => {

    console.log('Successfully registered user1 - secret:'+ secret);

    return fabric_ca_client.enroll({enrollmentID: 'user1', enrollmentSecret: secret});
}).then((enrollment) => {
  console.log('Successfully enrolled member user "user1" ');
  return fabric_client.createUser(
     {username: 'user1',
     mspid: 'Org1MSP',
     cryptoContent: { privateKeyPEM: enrollment.key.toBytes(), signedCertPEM: enrollment.certificate }
     });
}).then((user) => {
     member_user = user;

     return fabric_client.setUserContext(member_user);
}).then(()=>{
     console.log('User1 was successfully registered and enrolled and is ready to interact with the fabric network');

}).catch((err) => {
    console.error('Failed to register: ' + err);
    if(err.toString().indexOf('Authorization') > -1) {
        console.error('Authorization failures may be caused by having admin credentials from a previous CA instance.\n' +
        'Try again after deleting the contents of the store directory '+store_path);
    }
});
```

3) query.js

```javascript
'use strict';


//通过链码查询

var Fabric_Client = require('fabric-client');
var path = require('path');
var util = require('util');
var os = require('os');


var fabric_client = new Fabric_Client();


var channel = fabric_client.newChannel('mychannel');
var peer = fabric_client.newPeer('grpc://localhost:7051');
channel.addPeer(peer);


var member_user = null;
var store_path = path.join(__dirname, 'hfc-key-store');
console.log('Store path:' + store_path);
var tx_id = null;


var query = async (chaincodeId, fcn, args) => {
    try {
        var state_store = await Fabric_Client.newDefaultKeyValueStore({path: store_path});
        fabric_client.setStateStore(state_store);
        var crypto_suite = Fabric_Client.newCryptoSuite();

        var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
        crypto_suite.setCryptoKeyStore(crypto_store);
        fabric_client.setCryptoSuite(crypto_suite);
        var user_from_store = await fabric_client.getUserContext('user1', true)
        if (user_from_store && user_from_store.isEnrolled()) {
            console.log('Successfully loaded user1 from persistence');
            member_user = user_from_store;
        } else {
            throw new Error('Failed to get user1.... run registerUser.js');
        }

        //更改这里
        const request = {
            chaincodeId: chaincodeId,
            fcn: fcn,
            args: args
        };

        var query_responses = await channel.queryByChaincode(request);
        console.log("Query has completed, checking results");

        if (query_responses && query_responses.length == 1) {
            if (query_responses[0] instanceof Error) {
                console.error("error from query = ", query_responses[0]);
                return "error from query = " + query_responses[0]
            } else {
                console.log("Response is ", query_responses[0].toString());
                return query_responses[0].toString()
            }
        } else {
            console.log("No payloads were returned from query");
            return "No payloads were returned from query"
        }
    } catch (err) {
        console.error('Failed to query successfully :: ' + err);
        return 'Failed to query successfully :: ' + err
    }
};

module.exports = query;

```

4）invoke.js

```javascript
'use strict';
/*
* Copyright IBM Corp All Rights Reserved
*
* SPDX-License-Identifier: Apache-2.0
*/
/*
 * Chaincode Invoke
 */

var Fabric_Client = require('fabric-client');
var path = require('path');
var util = require('util');
var os = require('os');

//
var fabric_client = new Fabric_Client();

// setup the fabric network
var channel = fabric_client.newChannel('mychannel');
var peer = fabric_client.newPeer('grpc://localhost:7051');
channel.addPeer(peer);
var order = fabric_client.newOrderer('grpc://localhost:7050')
channel.addOrderer(order);

//
var member_user = null;
var store_path = path.join(__dirname, 'hfc-key-store');
console.log('Store path:' + store_path);
var tx_id = null;


var invoke = async (chainId, chaincodeId, fcn, args) => {

// create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
    Fabric_Client.newDefaultKeyValueStore({
        path: store_path
    }).then((state_store) => {
        // assign the store to the fabric client
        fabric_client.setStateStore(state_store);
        var crypto_suite = Fabric_Client.newCryptoSuite();
        // use the same location for the state store (where the users' certificate are kept)
        // and the crypto store (where the users' keys are kept)
        var crypto_store = Fabric_Client.newCryptoKeyStore({path: store_path});
        crypto_suite.setCryptoKeyStore(crypto_store);
        fabric_client.setCryptoSuite(crypto_suite);

        // get the enrolled user from persistence, this user will sign all requests
        return fabric_client.getUserContext('user1', true);
    }).then((user_from_store) => {
        if (user_from_store && user_from_store.isEnrolled()) {
            console.log('Successfully loaded user1 from persistence');
            member_user = user_from_store;
        } else {
            throw new Error('Failed to get user1.... run registerUser.js');
        }

        // get a transaction id object based on the current user assigned to fabric client
        tx_id = fabric_client.newTransactionID();
        console.log("Assigning transaction_id: ", tx_id._transaction_id);

        // createCar chaincode function - requires 5 args, ex: args: ['CAR12', 'Honda', 'Accord', 'Black', 'Tom'],
        // changeCarOwner chaincode function - requires 2 args , ex: args: ['CAR10', 'Dave'],
        // must send the proposal to endorsing peers
        var request = {
            //targets: let default to the peer assigned to the client
            chaincodeId: chaincodeId,
            fcn: fcn,
            args: args,
            chainId: chainId,
            txId: tx_id
        };

        // send the transaction proposal to the peers
        return channel.sendTransactionProposal(request);
    }).then((results) => {
        var proposalResponses = results[0];
        var proposal = results[1];
        let isProposalGood = false;
        console.log(proposalResponses)
        if (proposalResponses && proposalResponses[0].response &&
            proposalResponses[0].response.status === 200) {
            isProposalGood = true;
            console.log('Transaction proposal was good');
        } else {
            console.error('Transaction proposal was bad');
        }
        if (isProposalGood) {
            console.log(util.format(
                'Successfully sent Proposal and received ProposalResponse: Status - %s, message - "%s"',
                proposalResponses[0].response.status, proposalResponses[0].response.message));

            // build up the request for the orderer to have the transaction committed
            var request = {
                proposalResponses: proposalResponses,
                proposal: proposal
            };

            // set the transaction listener and set a timeout of 30 sec
            // if the transaction did not get committed within the timeout period,
            // report a TIMEOUT status
            var transaction_id_string = tx_id.getTransactionID(); //Get the transaction ID string to be used by the event processing
            var promises = [];

            var sendPromise = channel.sendTransaction(request);
            promises.push(sendPromise); //we want the send transaction first, so that we know where to check status

            // get an eventhub once the fabric client has a user assigned. The user
            // is required bacause the event registration must be signed
            let event_hub = channel.newChannelEventHub(peer);

            // using resolve the promise so that result status may be processed
            // under the then clause rather than having the catch clause process
            // the status
            let txPromise = new Promise((resolve, reject) => {
                let handle = setTimeout(() => {
                    event_hub.unregisterTxEvent(transaction_id_string);
                    event_hub.disconnect();
                    resolve({event_status: 'TIMEOUT'}); //we could use reject(new Error('Trnasaction did not complete within 30 seconds'));
                }, 3000);
                event_hub.registerTxEvent(transaction_id_string, (tx, code) => {
                        // this is the callback for transaction event status
                        // first some clean up of event listener
                        clearTimeout(handle);

                        // now let the application know what happened
                        var return_status = {event_status: code, tx_id: transaction_id_string};
                        if (code !== 'VALID') {
                            console.error('The transaction was invalid, code = ' + code);
                            resolve(return_status); // we could use reject(new Error('Problem with the tranaction, event status ::'+code));
                        } else {
                            console.log('The transaction has been committed on peer ' + event_hub.getPeerAddr());
                            resolve(return_status);
                        }
                    }, (err) => {
                        //this is the callback if something goes wrong with the event registration or processing
                        reject(new Error('There was a problem with the eventhub ::' + err));
                    },
                    {disconnect: true} //disconnect when complete
                );
                event_hub.connect();

            });
            promises.push(txPromise);

            return Promise.all(promises);
        } else {
            console.error('Failed to send Proposal or receive valid response. Response null or status is not 200. exiting...');
            throw new Error('Failed to send Proposal or receive valid response. Response null or status is not 200. exiting...');
        }
    }).then((results) => {
        console.log('Send transaction promise and event listener promise have completed');
        // check the results in the order the promises were added to the promise all list
        if (results && results[0] && results[0].status === 'SUCCESS') {
            console.log('Successfully sent transaction to the orderer.');
            return 'Successfully sent transaction to the orderer.'
        } else {
            console.error('Failed to order the transaction. Error code: ' + results[0].status);
            return 'Failed to order the transaction. Error code: ' + results[0].status
        }

        if (results && results[1] && results[1].event_status === 'VALID') {
            console.log('Successfully committed the change to the ledger by the peer');
            return 'Successfully committed the change to the ledger by the peer'
        } else {
            console.log('Transaction failed to be committed to the ledger due to ::' + results[1].event_status);
            return 'Transaction failed to be committed to the ledger due to ::' + results[1].event_status
        }
    }).catch((err) => {
        console.error('Failed to invoke successfully :: ' + err);
        return 'Failed to invoke successfully :: ' + err
    });
};

module.exports = invoke;

```

5) app.js

```javascript
const express = require('express');
const app = express();
const query = require("./query");
const invoke = require("./invoke");
var bodyParser = require('body-parser');
const port = 3000;

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: false}));


//解决跨域
app.all('*',function (req, res, next) {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Content-Type, Content-Length, Authorization, Accept, X-Requested-With');
    res.header('Access-Control-Allow-Methods', 'PUT, POST, GET, DELETE, OPTIONS');
    if (req.method === 'OPTIONS') {
        res.send(200);
    }
    else {
        next();
    }
});

app.get('/queryAllFishes', async (req, res) => res.send(await query("fishcc","queryAllFishes",new Array("x"))));
app.get('/queryFish', async (req, res) => res.send(
    await query("fishcc", "queryFish", new Array(req.query.fish))
));
app.get('/queryHistory', async (req, res) => res.send(
    await query("fishcc", "queryHistory", new Array(req.query.fish))
));


/**
*  channel:mychannel
*  chaincode:fishcc
*  fcn:updateHolder
*  fish:FISH0
*  holder:1111
*/
app.post("/updateHolder", async (req, res)=>{
    res.send(await invoke(req.body.channel, req.body.chaincode, req.body.fcn, [req.body.fish, req.body.holder]))
});

/**
 * channel:mychannel
 * chaincode:fishcc
 * fcn:recordFish
 * fish:FISH40
 * holder:1111
 * location:1.0000, 4.0000
 * temperature:22
 * timestamp:111111
 * vessel:辽宁号
 * */
app.post("/recordFish", async (req, res)=>{
    res.send(
        await invoke(req.body.channel, req.body.chaincode, req.body.fcn,
            [req.body.fish, req.body.holder, req.body.location, req.body.temperature, req.body.timestamp, req.body.vessel]
        ));
});

app.listen(port, () => console.log(`Example app listening on port ${port}!`));
```

