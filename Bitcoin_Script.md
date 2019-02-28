# Assignment
This is assignment is structured to help you become familiar Bitcoin script.  Bitcoin script is the programming language of bitcoin. Although it is not Turing complete a large number of useful scripts can be created for many different use cases.   Through out this excercise there will be a number of ***Assignment Deliverable XX:*** . This will tell you what you need to turn in at the end of the assignment.  If there are more than one items required for that deliverable add a blank line between them. The format of the submitted work should be a text file with you name, date, assignment name, and UT ID. There should be sections of indicated by Assignment Deliverable XX: followed by the deliverable information.  For example:

Karl, Kreder            01/25/2019             Intro_to_Bitcoin_Command_Line            UTID

Assignment Deliverable 1:

mhpt1x5Ro8SSMAQrkXwCj4F6sZd1xL9nsT

H9v014McOc0+ESiyWMWJ2lBcGi+Vd+O0/IqSR0mdt15IEK7z158PKkD8JeRe5j4n29+Gyu2m5F3qPWwHQuAPS90= 

Hello, Bitcoin

Assignment Deliverable 2:

# Bitcoin Script
## Install btcdeb
The first thing that we are going to need to do is install a bitcoin script interpreter called btcdeb which stands for bitcoin debug.
Please go to the github repository and follow the instructions for either linux or mac.  Make sure you install dependencies before trying to install btcdeb.  Otherwise this process should be very straight forward.

https://github.com/kallewoof/btcdeb

## Signature script and pubKey Scripts

Before we get into bitcoin script we need to do a brief review on how tokens are moved on chain.  A token is encumbered by a locking script known as a pubKey script which can also be referred to as scriptPubKey.  To move a token, a signature script (scriptSig) must be presented which satisfies the conditions of the locking pubKey script.  Once a proper signature script is presented, the unspent transaction output (UTXO) is consumed with the signature script and one or more new UTXOs are created. These scripts are written using what is known as Bitcoin script and the scripts are evaluated by nodes running the Bitcoin blockchain. When a token is moved the scripts are evaluated in the following way.

```BASH
<sigScript><scriptPubKey>
```  

The Bitcoin interpretor is a stack based language that does not allow for loops and therefore is not Turing complete. The interpreter operates using reverse polish notation in that the operands come before the operator.  So in the simple case of computing 1 + 2, it would be written into the interpreter as 1 2 +. Operators can pop items off the stack as well as push results onto the stack.  Once a bitcoin script is complete it is considered to be valid if the result is greater than or equal to 1. Therefore, if the result is 0, the operation is not valid and the signature script does not get commited to the chain.  If the output is non-zero the chain updates the state according to the outcome of the script.

Therefore, in the above example the scriptSig is loaded into the stack first followed by the scriptPubKey.  Once fully evaluated if the scriptSig causes a non-zero result the transaciton is processed.


## Pays to PubKey (P2PK) script
P2PK scripts are the simplest form of a bitcoin encumbrance.  Tokens are locked by a script which requires a signature from a corresponding private key to spend the tokens.  The format of a P2PK script is:

```BASH
<Signature> <Public Key> OP_CHECKSIG
```

All early Bitcoin transactions used P2PK, however this method is presently deprecated and our tools no longer support it.  Therefore, instead of trying to make one of these transactions on our own, lets use btcdeb to checkout and validate the very first Bitcoin transaction evermade between Satoshi and Hal Finney.  This will help you to get more familiar with using block explorers as well as introduce you to using btcdeb.  The very first Bitcoin transaction has txid `f4184fc596403b9d638783cf57adfe4c75c605f6356fbc91338530e9831e9e16`. You can view this transaction using blockcypher https://live.blockcypher.com/btc/tx/f4184fc596403b9d638783cf57adfe4c75c605f6356fbc91338530e9831e9e16/ .

You can see that freshly mined Bitcoins were sent to two wallets.  Lets try and use btcdeb to validate this transaction.  To do so we will need to collect some information about the transaction.  The first thing that we will collect is the scriptPubKey.  This can be found by using the blockcypher api which is documented here http://blockcypher.github.io/documentation/ . We will want to lookup the json parsing of this transaction as well as getting our hands on the raw hex.  To do this we will use this url https://api.blockcypher.com/v1/btc/main/txs/f4184fc596403b9d638783cf57adfe4c75c605f6356fbc91338530e9831e9e16?includeHex=true . You should get something like this.

```BASH
{
  "block_hash": "00000000d1145790a8694403d4063f323d499e655c83426834d4ce2f8dd4a2ee",
  "block_height": 170,
  "block_index": 1,
  "hash": "f4184fc596403b9d638783cf57adfe4c75c605f6356fbc91338530e9831e9e16",
  "hex": "0100000001c997a5e56e104102fa209c6a852dd90660a20b2d9c352423edce25857fcd3704000000004847304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901ffffffff0200ca9a3b00000000434104ae1a62fe09c5f51b13905f07f06b99a2f7159b2225f374cd378d71302fa28414e7aab37397f554a7df5f142c21c1b7303b8a0626f1baded5c72a704f7e6cd84cac00286bee0000000043410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac00000000",
  "addresses": [
    "12cbQLTFMXRnSzktFkuoG3eHoMeFtpTu3S",
    "1Q2TWHE3GMdB6BZKafqwxXtWAWgFt5Jvm3"
  ],
  "total": 5000000000,
  "fees": 0,
  "size": 275,
  "preference": "low",
  "confirmed": "2009-01-12T03:30:25Z",
  "received": "2009-01-12T03:30:25Z",
  "ver": 1,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 2,
  "confirmations": 564339,
  "confidence": 1,
  "inputs": [
    {
      "prev_hash": "0437cd7f8525ceed2324359c2d0ba26006d92d856a9c20fa0241106ee5a597c9",
      "output_index": 0,
      "script": "47304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901",
      "output_value": 5000000000,
      "sequence": 4294967295,
      "addresses": [
        "12cbQLTFMXRnSzktFkuoG3eHoMeFtpTu3S"
      ],
      "script_type": "pay-to-pubkey",
      "age": 9
    }
  ],
  "outputs": [
    {
      "value": 1000000000,
      "script": "4104ae1a62fe09c5f51b13905f07f06b99a2f7159b2225f374cd378d71302fa28414e7aab37397f554a7df5f142c21c1b7303b8a0626f1baded5c72a704f7e6cd84cac",
      "spent_by": "ea44e97271691990157559d0bdd9959e02790c34db6c006d779e82fa5aee708e",
      "addresses": [
        "1Q2TWHE3GMdB6BZKafqwxXtWAWgFt5Jvm3"
      ],
      "script_type": "pay-to-pubkey"
    },
    {
      "value": 4000000000,
      "script": "410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac",
      "spent_by": "a16f3ce4dd5deb92d98ef5cf8afeaf0775ebca408f708b2146c4fb42b41e14be",
      "addresses": [
        "12cbQLTFMXRnSzktFkuoG3eHoMeFtpTu3S"
      ],
      "script_type": "pay-to-pubkey"
    }
  ]
}

```


 You can simply replace the txid in the above url to get the same result for any transaction.  Alternating if you are looking for something on the testnet you can use https://api.blockcypher.com/v1/btc/test3/txs/6be71dca3d789d8b7c846a8166138087ce87c1ec1b96b286f7933a85bd378831?includeHex=true .

If we inspect this transaction we can see that a coinbase UTXO of 50 BTC was used to send 10 BTC and got 40 BTC in change.  Lets follow the 40 BTC output and validate it with btcdeb.  We will need to collect the raw transaction hex corresponsponding to this 40 BTC.  We can do this by following the (spent) link https://live.blockcypher.com/btc/tx/a16f3ce4dd5deb92d98ef5cf8afeaf0775ebca408f708b2146c4fb42b41e14be/#spentby-f4184fc596403b9d638783cf57adfe4c75c605f6356fbc91338530e9831e9e16-1 and then taking that TXID to lookup the hex of the transaction here. https://api.blockcypher.com/v1/btc/main/txs/a16f3ce4dd5deb92d98ef5cf8afeaf0775ebca408f708b2146c4fb42b41e14be?includeHex=true

```BASH

{
  "block_hash": "00000000dc55860c8a29c58d45209318fa9e9dc2c1833a7226d86bc465afc6e5",
  "block_height": 181,
  "block_index": 1,
  "hash": "a16f3ce4dd5deb92d98ef5cf8afeaf0775ebca408f708b2146c4fb42b41e14be",
  "hex": "0100000001169e1e83e930853391bc6f35f605c6754cfead57cf8387639d3b4096c54f18f40100000048473044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01ffffffff0200ca9a3b00000000434104b5abd412d4341b45056d3e376cd446eca43fa871b51961330deebd84423e740daa520690e1d9e074654c59ff87b408db903649623e86f1ca5412786f61ade2bfac005ed0b20000000043410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac00000000",
  "addresses": [
    "12cbQLTFMXRnSzktFkuoG3eHoMeFtpTu3S",
    "1DUDsfc23Dv9sPMEk5RsrtfzCw5ofi5sVW"
  ],
  "total": 4000000000,
  "fees": 0,
  "size": 275,
  "preference": "low",
  "confirmed": "2009-01-12T06:02:13Z",
  "received": "2009-01-12T06:02:13Z",
  "ver": 1,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 2,
  "confirmations": 564328,
  "confidence": 1,
  "inputs": [
    {
      "prev_hash": "f4184fc596403b9d638783cf57adfe4c75c605f6356fbc91338530e9831e9e16",
      "output_index": 1,
      "script": "473044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01",
      "output_value": 4000000000,
      "sequence": 4294967295,
      "addresses": [
        "12cbQLTFMXRnSzktFkuoG3eHoMeFtpTu3S"
      ],
      "script_type": "pay-to-pubkey",
      "age": 170
    }
  ],
  "outputs": [
    {
      "value": 1000000000,
      "script": "4104b5abd412d4341b45056d3e376cd446eca43fa871b51961330deebd84423e740daa520690e1d9e074654c59ff87b408db903649623e86f1ca5412786f61ade2bfac",
      "addresses": [
        "1DUDsfc23Dv9sPMEk5RsrtfzCw5ofi5sVW"
      ],
      "script_type": "pay-to-pubkey"
    },
    {
      "value": 3000000000,
      "script": "410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac",
      "spent_by": "591e91f809d716912ca1d4a9295e70c3e78bab077683f79350f101da64588073",
      "addresses": [
        "12cbQLTFMXRnSzktFkuoG3eHoMeFtpTu3S"
      ],
      "script_type": "pay-to-pubkey"
    }
  ]
}

```

The simplest way we can use btcdeb to validate this transaction is simply by inputing the incoming transaction hex and the transaction hex as follows:

```BASH
btcdeb --txin=01000000010000000000000000000000000000000000000000000000000000000000000000ffffffff0704ffff001d0134ffffffff0100f2052a0100000043410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac00000000 --tx=0100000001c997a5e56e104102fa209c6a852dd90660a20b2d9c352423edce25857fcd3704000000004847304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901ffffffff0200ca9a3b00000000434104ae1a62fe09c5f51b13905f07f06b99a2f7159b2225f374cd378d71302fa28414e7aab37397f554a7df5f142c21c1b7303b8a0626f1baded5c72a704f7e6cd84cac00286bee0000000043410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac00000000
```

Once this script starts running we can use the command `step` to move through the script.  On the left side we will see the script that is yet to be executed and on the right we will be able to see the stack. Each time we step we will also see the push, pop, and operations performed by each opcode.  This will allow ourselves to trace the execution of the script step by step and see if anything goes wrong.  Stepping throught the script will produce the following:

```BASH
script                                                             |  stack
-------------------------------------------------------------------+--------
304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548a... |
<<< scriptPubKey >>>                                               |
0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909... |
OP_CHECKSIG                                                        |
#0000 304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901
btcdeb> step
		<> PUSH stack 304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
<<< scriptPubKey >>>                                               | 304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548a...
0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909... |
OP_CHECKSIG                                                        |
<<< scriptPubKey >>>
btcdeb> step
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909... | 304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548a...
OP_CHECKSIG                                                        |
#0002 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3
btcdeb> step
		<> PUSH stack 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
OP_CHECKSIG                                                        | 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909...
                                                                   | 304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548a...
#0003 OP_CHECKSIG
btcdeb> step
		<> POP  stack
		<> POP  stack
		<> PUSH stack 01
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
                                                                   |                                                                 01
```

Looking at the ouput we can observe that the stack started empty. Then the sigScript was added. Then the scriptPubKey was added.  Then the top 2 stack items were consumed by OP_CHECKSIG which produced a result of 01 and pushed it to the stack.  This script evaluates as true and is a valid script.  Now although this is insightful, the fact that we started with just hex is not very helpful when trying to work in bitcoin script.  To understand what is going on in the hex we would have to dig through a decode all of the hex.  An alternate way is that we can decode the hex using bitcoin-cli `decoderawtransaction` or take pieces from the decoded json returned from blockcypher and then use btcdeb to observe our decoded transaction being executed.  

Lets go ahead and build the bitcoin script manually using the outputs from blockcypher.  To do this we will grab the scriptSig from the "script" field under "input" on the second json ouptut.  The reason we are using the second TxId is because this is the one that produced a signature moving the tokens from the first TxId.  This value is `473044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01`. 

However, we need to be careful here because blockcypher has presented this as encoded hex which means the first byte is an opcode indicating we are pushing a block of data of 71 bytes.  We will also need to find the scriptPubKey from the first json output. This is under "script" under "outputs".  We will use the second value as we are validating the second ouput of 40 BTC. This value is `410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac`. This value will also need to be interpreted.  The first byte is indicating pushing 65 bytes of data and we have 66 subsequent bytes meaning the last byte is also an opcode.  To figure out what this means we can look up `ac` at https://en.bitcoin.it/wiki/Script . Here we can find that this corresponds to the opcode OP_CHECKSIG.

Therefore we have:
```BASH

<sigScript> = 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01
<pubKeyScript> = 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3 OP_CHECKSIG
```

Lets try running this using btcdeb. To do that we use the following command.

```BASH
> $ btcdeb '[ 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3 OP_CHECKSIG]'
btcdeb -- type `btcdeb -h` for start up options
valid script
3 op script loaded. type `help` for usage information
script                                                             |  stack
-------------------------------------------------------------------+--------
3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1... |
0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909... |
OP_CHECKSIG                                                        |
#0000 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01
btcdeb> step
		<> PUSH stack 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909... | 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1...
OP_CHECKSIG                                                        |
#0001 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3
btcdeb> step
		<> PUSH stack 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
OP_CHECKSIG                                                        | 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909...
                                                                   | 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1...
#0002 OP_CHECKSIG
btcdeb> step
error: Signature must be zero for failed CHECK(MULTI)SIG operation (is this a historical transaction from before NULLFAIL enforcement? Try with --modify-flags=-NULLFAIL)
```

Notice how btcdeb faild to check the signature?  That is because btcdeb is unaware of the data (the transaction) which was signed and therefore cannot validate the signature.  To make btcdeb aware we can set the variable tx to the raw hex and then btcdeb will know how to evaulate the signature correctly.  This can be done as follows:


```BASH
> $ btcdeb --tx=0100000001169e1e83e930853391bc6f35f605c6754cfead57cf8387639d3b4096c54f18f40100000048473044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01ffffffff0200ca9a3b00000000434104b5abd412d4341b45056d3e376cd446eca43fa871b51961330deebd84423e740daa520690e1d9e074654c59ff87b408db903649623e86f1ca5412786f61ade2bfac005ed0b20000000043410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac00000000 '[ 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3 OP_CHECKSIG]'
btcdeb -- type `btcdeb -h` for start up options
got transaction a16f3ce4dd5deb92d98ef5cf8afeaf0775ebca408f708b2146c4fb42b41e14be:
CTransaction(hash=a16f3ce4dd, ver=1, vin.size=1, vout.size=2, nLockTime=0)
    CTxIn(COutPoint(f4184fc596, 1), scriptSig=473044022027542a94d6646c)
    CScriptWitness()
    CTxOut(nValue=10.00000000, scriptPubKey=4104b5abd412d4341b45056d3e376c)
    CTxOut(nValue=30.00000000, scriptPubKey=410411db93e1dcdb8a016b49840f8c)

valid script
3 op script loaded. type `help` for usage information
script                                                             |  stack
-------------------------------------------------------------------+--------
3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1... |
0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909... |
OP_CHECKSIG                                                        |
#0000 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01
btcdeb> step
		<> PUSH stack 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1171a3212e02203baf203c6e7b80ebd3e588628466ea28be572fe1aaa3f30947da4763dd3b3d2b01
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909... | 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1...
OP_CHECKSIG                                                        |
#0001 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3
btcdeb> step
		<> PUSH stack 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
OP_CHECKSIG                                                        | 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909...
                                                                   | 3044022027542a94d6646c51240f23a76d33088d3dd8815b25e9ea18cac67d1...
#0002 OP_CHECKSIG
btcdeb> step
		<> POP  stack
		<> POP  stack
		<> PUSH stack 01
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
                                                                   |                                                                 01


```

This evaluates to one again so now we know that the transaction was correct and the script that was used to validate this transaction was indeed a P2PK of the form `<Signature> <Public Key> OP_CHECKSIG`.  We won't have you try and build a P2PK because it would require some other tools and they are not currently very useful because of the introduction of P2PKH. 

## Pays To PubKey Hash (P2PKH) script
P2PKH was introduced because it allows people to not reveal their public key prior to spending an output.  If there ever is a weakness in elliptic curve cryptography and the public keys that are encumbering all the funds are chain are publically known, this could allow anyone that is able to leverage the ECC exploit to move all on chain balances.  By using a hash to encumber funds rather than an public address there would also need to be a compromise to SHA256 before money locked on chain can be moved. Therefore these are currently the most common scripts used in bitcoin today.

Lets go and look at the simple single input to single ouput transactions that we made in the previous assignment.  The example in the tutorial was TxID 

btcdeb --tx=020000000145f57f53388b6376ea5c37fae76178a445e7474970d38bd357991fcf5055baaf000000006b483045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794a011dd5b912022070eb93341ea04304eaa409533757add60ccf4df44075cd5777b5379f5e6d5b2e0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0ffffffff01cc5f85000000000017a914e995733209fce5e8b54c4e2744aff28015bcd3f98700000000 --txin=0200000001e675a287b8143df4ab739e469b0aab00adb9ccd55b6846f5399fda64da410fd9000000006b483045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0ffffffff01dc868500000000001976a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac00000000

btcdeb --tx=0.08750812:020000000145f57f53388b6376ea5c37fae76178a445e7474970d38bd357991fcf5055baaf000000006b483045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794a011dd5b912022070eb93341ea04304eaa409533757add60ccf4df44075cd5777b5379f5e6d5b2e0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0ffffffff01cc5f85000000000017a914e995733209fce5e8b54c4e2744aff28015bcd3f98700000000 '[OP_DUP OP_HASH160 ca073a588b2ea809fcfe4aae9f89fa73acf41177 OP_EQUALVERIFY OP_CHECKSIG]' 3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794a011dd5b912022070eb93341ea04304eaa409533757add60ccf4df44075cd5777b5379f5e6d5b2e01 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0


Before we begin writing our own bitcoin scripts lets look at a couple of examples that exist by using blockcypher. This will make you dig into the component pieces of a transaction more and give you your first example of a successfully running bitcoin transaction script.

https://api.blockcypher.com/v1/btc/test3/txs/afba5550cf1f9957d38bd3704947e745a47861e7fa375cea76638b38537ff545?includeHex=true

{
  "block_hash": "0000000000000068163f3e1e1932d39d470a9e7417a06d116b091b6f18501fe0",
  "block_height": 1481272,
  "block_index": 1,
  "hash": "afba5550cf1f9957d38bd3704947e745a47861e7fa375cea76638b38537ff545",
  "hex": "0200000001e675a287b8143df4ab739e469b0aab00adb9ccd55b6846f5399fda64da410fd9000000006b483045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0ffffffff01dc868500000000001976a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac00000000",
  "addresses": [
    "mywBT1RBVqhzCTQpc3pycbJkM7v1tagQdA"
  ],
  "total": 8750812,
  "fees": 10000,
  "size": 192,
  "preference": "high",
  "relayed_by": "54.38.142.36:18333",
  "confirmed": "2019-02-24T19:27:05Z",
  "received": "2019-02-24T19:14:37.379Z",
  "ver": 2,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 1,
  "confirmations": 702,
  "confidence": 1,
  "inputs": [
    {
      "prev_hash": "d90f41da64da9f39f546685bd5ccb9ad00ab0a9b469e73abf43d14b887a275e6",
      "output_index": 0,
      "script": "483045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0",
      "output_value": 8760812,
      "sequence": 4294967295,
      "addresses": [
        "mywBT1RBVqhzCTQpc3pycbJkM7v1tagQdA"
      ],
      "script_type": "pay-to-pubkey-hash",
      "age": 1457005
    }
  ],
  "outputs": [
    {
      "value": 8750812,
      "script": "76a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac",
      "spent_by": "b3d970bbea3bc92c6b00d7cfdfc447ad35aa14d27b3e699e4b9cbbe2113f53b3",
      "addresses": [
        "mywBT1RBVqhzCTQpc3pycbJkM7v1tagQdA"
      ],
      "script_type": "pay-to-pubkey-hash"
    }
  ]
}

Unfortunately blockcypher does not show us the interpreted scripts, but we can figure it out pretty easily by hand.  If you go to https://en.bitcoin.it/wiki/Script you will find a list of currently supported as well as deprecated script opcodes.  Opcodes are the commands which are avialable to use in bitcoin script and they will always be 1 hex byte.  So if we look at our locking script for the transaction:

```BASH
76a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac

or

Byte #
01 02 03 04 05 06 07 08 09 10 11 12 13 14 15 16 17 18 19 20 21 22 23 24 25
Hex
76 a9 14 ca 07 3a 58 8b 2e a8 09 fc fe 4a ae 9f 89 fa 73 ac f4 11 77 88 ac
```

We can start to interpret it by parsing it from left to right.  The first byte is 0x76.  If we look at the bitcoin opcodes 0x76 is OP_DUP. The second byte is 0xa9 which is OP_HASH160, the third byte is 0x14 which corresponds to push data of length 0x14 which in decimal is 20 bytes.  So we know that bytes 04 through 23 are a payload which in this case corresponds to the MD160 HASH which we are trying to match.  Byte 24 is 0x88 which is OP_EQUALVERIFY and 0xac is OP_CHECKSIG.  So if we put that together we get.

```BASH
OP_DUP OP_HASH160 PUSH(20) ca073a588b2ea809fcfe4aae9f89fa73acf41177 OP_EQUALVERIFY OP_CHECKSIG
```

Looking at this we can see that this is an example of a standard P2PKH script.  So lets try and create a signature script which will unlock this transaction and use btcdeb to check it and make sure it works before broadcasting it to the network.

 {
    "txid": "d90f41da64da9f39f546685bd5ccb9ad00ab0a9b469e73abf43d14b887a275e6",
    "vout": 0,
    "address": "mywBT1RBVqhzCTQpc3pycbJkM7v1tagQdA",
    "account": "",
    "scriptPubKey": "76a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac",
    "amount": 0.08760812,
    "confirmations": 24251,
    "spendable": true,
    "solvable": true,
    "safe": true
  },

To create the signature script we can leverage `createrawtransaction` followed by `signrawtransaction`.  Once the signed transaction is created we can use `decoderawtransaction` to see the transaction that we have created but not yet broadcast.

```BASH
> $ bc decoderawtransaction 0200000001e675a287b8143df4ab739e469b0aab00adb9ccd55b6846f5399fda64da410fd9000000006b483045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0ffffffff01dc868500000000001976a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac00000000
{
  "txid": "afba5550cf1f9957d38bd3704947e745a47861e7fa375cea76638b38537ff545",
  "hash": "afba5550cf1f9957d38bd3704947e745a47861e7fa375cea76638b38537ff545",
  "version": 2,
  "size": 192,
  "vsize": 192,
  "locktime": 0,
  "vin": [
    {
      "txid": "d90f41da64da9f39f546685bd5ccb9ad00ab0a9b469e73abf43d14b887a275e6",
      "vout": 0,
      "scriptSig": {
        "asm": "3045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb[ALL] 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0",
        "hex": "483045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0"
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 0.08750812,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 ca073a588b2ea809fcfe4aae9f89fa73acf41177 OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "mywBT1RBVqhzCTQpc3pycbJkM7v1tagQdA"
        ]
      }
    }
  ]
}

```

If you look in the "scriptSig" filed you will find the signature as well as the pubkey which will allow you to unlock and spend the transaction. 

The data in this filed can be interpreted as follows:
```BASH
<signature> = 3045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb 
<pubkey> = 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
```

This should be a valid unlocking script for a P2PKH. The simple way to check this works prior to broadcasting the transaction is to use `--tx=` and `--txin=` prior to broadcasting the transaction.  Lets look at each step and make sure we understand what is happening.

```BASH
> $ btcdeb --tx=020000000145f57f53388b6376ea5c37fae76178a445e7474970d38bd357991fcf5055baaf000000006b483045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794a011dd5b912022070eb93341ea04304eaa409533757add60ccf4df44075cd5777b5379f5e6d5b2e0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0ffffffff01cc5f85000000000017a914e995733209fce5e8b54c4e2744aff28015bcd3f98700000000 --txin=0200000001e675a287b8143df4ab739e469b0aab00adb9ccd55b6846f5399fda64da410fd9000000006b483045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0ffffffff01dc868500000000001976a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac00000000
btcdeb -- type `btcdeb -h` for start up options
got transaction b3d970bbea3bc92c6b00d7cfdfc447ad35aa14d27b3e699e4b9cbbe2113f53b3:
CTransaction(hash=b3d970bbea, ver=2, vin.size=1, vout.size=1, nLockTime=0)
    CTxIn(COutPoint(afba5550cf, 0), scriptSig=483045022100afe2d4708627)
    CScriptWitness()
    CTxOut(nValue=0.08740812, scriptPubKey=a914e995733209fce5e8b54c4e2744)

got input tx #0 afba5550cf1f9957d38bd3704947e745a47861e7fa375cea76638b38537ff545:
CTransaction(hash=afba5550cf, ver=2, vin.size=1, vout.size=1, nLockTime=0)
    CTxIn(COutPoint(d90f41da64, 0), scriptSig=483045022100ac7169d5d135)
    CScriptWitness()
    CTxOut(nValue=0.08750812, scriptPubKey=76a914ca073a588b2ea809fcfe4aae)

input tx index = 0; tx input vout = 0; value = 8750812
8 op script loaded. type `help` for usage information
script                                                             |  stack
-------------------------------------------------------------------+--------
3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794... |
038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0 |
<<< scriptPubKey >>>                                               |
OP_DUP                                                             |
OP_HASH160                                                         |
ca073a588b2ea809fcfe4aae9f89fa73acf41177                           |
OP_EQUALVERIFY                                                     |
OP_CHECKSIG                                                        |
#0000 3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794a011dd5b912022070eb93341ea04304eaa409533757add60ccf4df44075cd5777b5379f5e6d5b2e01
btcdeb> step
    <> PUSH stack 3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794a011dd5b912022070eb93341ea04304eaa409533757add60ccf4df44075cd5777b5379f5e6d5b2e01
```

The <sigScript> is queued followed by the <scriptPubKey> on the left under the script column.  The stack colum is on the right.  We can see that the stack started empty.  The interpretor starts running the script by evaluating the items that have been placed into the script column one-by-one.  The first item is a data field so it is pushed to the stack.

```BASH
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0 | 3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794...
<<< scriptPubKey >>>                                               |
OP_DUP                                                             |
OP_HASH160                                                         |
ca073a588b2ea809fcfe4aae9f89fa73acf41177                           |
OP_EQUALVERIFY                                                     |
OP_CHECKSIG                                                        |
#0001 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
btcdeb>
    <> PUSH stack 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
```

The second item, the pubkey, is also a data field so it is pushed to the stack

```BASH
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
OP_DUP                                                             | 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
OP_HASH160                                                         | 3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794...
ca073a588b2ea809fcfe4aae9f89fa73acf41177                           |
OP_EQUALVERIFY                                                     |
OP_CHECKSIG                                                        |
#0003 OP_DUP
btcdeb>
    <> PUSH stack 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
```

The next item is a command OP_DUP which takes the top item in the stack, duplicates it, and then pushes the result. So this one command interacts with the stack 1 time when it pushes the duplicated entry, which we can see by the `<> PUSH stack 38e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0`

```BASH
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
OP_HASH160                                                         | 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
ca073a588b2ea809fcfe4aae9f89fa73acf41177                           | 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
OP_EQUALVERIFY                                                     | 3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794...
OP_CHECKSIG                                                        |
#0004 OP_HASH160
btcdeb>
    <> POP  stack
    <> PUSH stack ca073a588b2ea809fcfe4aae9f89fa73acf41177
```

The next item is also a command OP_HASH160.  This command will pop an entry from the stack, computed the MD160 hash, and the push the result back onto the stack.  This command interacts with the stack 2 times as we can see with the `POP stack` and `PUSH stack` entries.

```BASH
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
ca073a588b2ea809fcfe4aae9f89fa73acf41177                           |                           ca073a588b2ea809fcfe4aae9f89fa73acf41177
OP_EQUALVERIFY                                                     | 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
OP_CHECKSIG                                                        | 3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794...
#0005 ca073a588b2ea809fcfe4aae9f89fa73acf41177
btcdeb>
    <> PUSH stack ca073a588b2ea809fcfe4aae9f89fa73acf41177
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
OP_EQUALVERIFY                                                     |                           ca073a588b2ea809fcfe4aae9f89fa73acf41177
OP_CHECKSIG                                                        |                           ca073a588b2ea809fcfe4aae9f89fa73acf41177
                                                                   | 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
                                                                   | 3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794...
#0006 OP_EQUALVERIFY
btcdeb>
    <> POP  stack
    <> POP  stack
    <> PUSH stack 01
    <> POP  stack
```

The next item is also a command which is OP_EQUALVERIFY.  This pops the top two items off the stack, compares them, pushes the result to the stack.  It then pops the top item if the result is one, else it stops the script with a return of 0.  If we used OP_EQUAL rather than OP_EQUALVERIFY, the operation would end after pushed 01 to the stack.  We can see that this operation interacts with the stack 4 times.

```BASH
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
OP_CHECKSIG                                                        | 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0
                                                                   | 3045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794...
#0007 OP_CHECKSIG
btcdeb>
    <> POP  stack
    <> POP  stack
    <> PUSH stack 01
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
                                                                   |                                                                 01

```

Finally, we have OP_CHECKSIG.  This operation will check and make sure that the signature of the transaction is valid against the public key.  To do this the script behind the scenes needs to compute the transaction hash to enable it to validate the signature.  If for some reason the hash of the tx is different this will return false.

We have now succesfully used btcdeb to check a transaction prior to sending it to the network!

### Debugging Errors in btcdeb

Getting a false return on an OP_CHECKSIG can be really frustrating because you can have the exact same stack, and get two different results.  To help understand why this is happening you can use a couple debug commands included in btcdeb.  These are `DEBUG_SIGNING` and `DEBUG_SIGHASH` and they are used like this.

```BASH
DEBUG_SIGNING=1 DEBUG_SIGHASH=1 btcdeb --tx=020000000145f57f53388b6376ea5c37fae76178a445e7474970d38bd357991fcf5055baaf000000006b483045022100afe2d4708627948004b00a2c076c13940b614f898a718655ce794a011dd5b912022070eb93341ea04304eaa409533757add60ccf4df44075cd5777b5379f5e6d5b2e0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0ffffffff01cc5f85000000000017a914e995733209fce5e8b54c4e2744aff28015bcd3f98700000000 --txin=0200000001e675a287b8143df4ab739e469b0aab00adb9ccd55b6846f5399fda64da410fd9000000006b483045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb0121038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0ffffffff01dc868500000000001976a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac00000000
```

This can be incrediably helpful as you get into writing scripts, because if you drop a byte by accident, or mistype a tx value by one digit, this problem will arise, and theses functions will provide output to allow you to quickly track them down.

## Convert P2SH to P2PKH

Now that we have a feel for btcdeb and what it can do, lets go ahead and try and create a custom bit of script and broadcast it to the network.  We can do this more easily by leveraging the functions of btcdeb to do the heavy lifting of creating transactions and signatures.  This will allow us to only have to deal with the bytecode associated with the scriptPubKey itself.

The simplest thing that we can do is change one standard script type to another standard script type but do it manually.

Lets go ahead and create a new key using `NEW_ADDRESS_11=$(bc getnewaddress)`.  This will create a P2SH key by defualt.  Then lets go ahead and pick a single input and create a transaction sending money to this address using `createrawtransaction`.

Once we have this lets inspect our raw transaction:

```BASH
{
  "txid": "c826fe4e8ed1da337644d8073fb74ed0646bac9148f86421f8623c8500c47cad",
  "hash": "c826fe4e8ed1da337644d8073fb74ed0646bac9148f86421f8623c8500c47cad",
  "version": 2,
  "size": 83,
  "vsize": 83,
  "locktime": 0,
  "vin": [
    {
      "txid": "70952c0fc94e596ddf9779167a1959d299ca6edb39a76b71fba2674aecc7fe83",
      "vout": 0,
      "scriptSig": {
        "asm": "",
        "hex": ""
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 0.09576917,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_HASH160 3fc0a0e29729146fdf3b06eb2face623cb023464 OP_EQUAL",
        "hex": "a9143fc0a0e29729146fdf3b06eb2face623cb02346487",
        "reqSigs": 1,
        "type": "scripthash",
        "addresses": [
          "2My4KKRqrqPwkc9c7RjnHUH31dLCjXE56Bo"
        ]
      }
    }
  ]
}

```

To turn this into a P2PKH we will need to do a couple of things.  We will need to modify the scriptPubKey to confirm to the P2PKH standard as well as compute a new MD160 hash.  To do this we will need to open up our wallet.dat file and search for the public key that we are using as the sending address.  We should copy the public key which in this case looks like `cR6PrMEcvT7RTamuW63oNCYc3AD8UznFbBQuPttXoBbuNJfaUiBT`.  If you get a value starting with 0014 you are finding the wrong value.  Once we have the pubkey we will need to find its MD160 hash which we can do quicly and conveniently using a quick script in btcdeb.

```BASH
> $ btcdeb '[cR6PrMEcvT7RTamuW63oNCYc3AD8UznFbBQuPttXoBbuNJfaUiBT OP_HASH160]'
btcdeb -- type `btcdeb -h` for start up options
valid script
2 op script loaded. type `help` for usage information
script                                                             |  stack
-------------------------------------------------------------------+--------
63523650724d45637654375254616d755736336f4e43596333414438557a6e4... |
OP_HASH160                                                         |
#0000 63523650724d45637654375254616d755736336f4e43596333414438557a6e4662425175507474586f4262754e4a666155694254
btcdeb>
btcdeb> stack
- empty stack -
btcdeb> step
    <> PUSH stack 63523650724d45637654375254616d755736336f4e43596333414438557a6e4662425175507474586f4262754e4a666155694254
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
OP_HASH160                                                         | 63523650724d45637654375254616d755736336f4e43596333414438557a6e4...
#0001 OP_HASH160
btcdeb>
    <> POP  stack
    <> PUSH stack 15e1ccc506e49174362a5c04c67e169a5ecef4de
script                                                             |                                                             stack
-------------------------------------------------------------------+-------------------------------------------------------------------
                                                                   |                           15e1ccc506e49174362a5c04c67e169a5ecef4de
```

This gives us the MD160 Hash of `15e1ccc506e49174362a5c04c67e169a5ecef4de`

Now that we have the MD160 we will need to disect our raw transaction and modify the byte code.

```BASH
020000000183fec7ec4a67a2fb716ba739db6eca99d259197a167997df6d594ec90f2c95700000000000ffffffff01d521920000000000

17      //Length of scriptPubKey
a9      //OP_HASH160
14      //Length of hash in bytes
3fc0a0e29729146fdf3b06eb2face623cb023464  //Redeem script hash
87      //OP_EQUAL
00000000 //TIMELOCK
```

We don't need to worry about the first half of the transaction for now.  The second half is the scriptPubKey which I have parsed by referring to the bitcoin op_codes.

I can do the following to modify it to make it a standard P2PKH script.

```BASH
020000000183fec7ec4a67a2fb716ba739db6eca99d259197a167997df6d594ec90f2c95700000000000ffffffff01d521920000000000
19     //Length of scriptPubKey
76     //OP_HASH160
a9     //OP_HASH160
14     //Length of hash in bytes
15e1ccc506e49174362a5c04c67e169a5ecef4de  //MD160 hash of publickey
88     //OP_EQUAL
ac     //OP_EQUAL
00000000 //TIMELOCK
```

I can then inspect my new transaction using decode as below.

```BASH
> $ bc decoderawtransaction 020000000183fec7ec4a67a2fb716ba739db6eca99d259197a167997df6d594ec90f2c95700000000000ffffffff01d5219200000000001976a91415e1ccc506e49174362a5c04c67e169a5ecef4de88ac00000000
{
  "txid": "9aea31a1a1e089414cca398ad53fefec90d1514cb46eec0f86d279a1fe945d27",
  "hash": "9aea31a1a1e089414cca398ad53fefec90d1514cb46eec0f86d279a1fe945d27",
  "version": 2,
  "size": 85,
  "vsize": 85,
  "locktime": 0,
  "vin": [
    {
      "txid": "70952c0fc94e596ddf9779167a1959d299ca6edb39a76b71fba2674aecc7fe83",
      "vout": 0,
      "scriptSig": {
        "asm": "",
        "hex": ""
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 0.09576917,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 15e1ccc506e49174362a5c04c67e169a5ecef4de OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a91415e1ccc506e49174362a5c04c67e169a5ecef4de88ac",
        "reqSigs": 1,
        "type": "pubkeyhash",
        "addresses": [
          "mhWeyw5kUN71Q1Ys32D2hamYDvwXE5RB2e"
        ]
      }
    }
  ]
}
```

I can see this is a valid transaction and it is interpreted correctly.  Therefore lets go ahead and sign and send it. I end up getting the TxID `d575e3ec984fd6b777a26944a10ae174e0a1e76c0087a82aae0d46057be322c9`.  I can go ahead and look it up on blockcypher.  Great, but there is a problem.  This transaction will not show up in your wallet.   The wallet software has not recorded a P2PKH key in your wallet against this public key, only a P2SH key.  Therefore, we will need to find the pubkey in your wallet.dat file and then remove the address that starts with a 2 and replace it with recieve address that is displayed on blockcypher that starts with an m.  Once this is done and the file is saved you should be able to do `bc listunspent` and the transaction will appear.

pubkey
cR6PrMEcvT7RTamuW63oNCYc3AD8UznFbBQuPttXoBbuNJfaUiBT

Recieve address
mhWeyw5kUN71Q1Ys32D2hamYDvwXE5RB2e

# Modified Bitcoin Script
Although that was alot of fun and a complete pain in the butt, what else can we do with Bitcoin script besides changing from one standard script to another?  Lets experiment a little bit and see what happens.  Instead of just doing a P2PKH lets explore some of the other functions that are available to us in scripting.  Lets do a little simple math maybe 1 + 2 =3. I am going to append this after the OP_EQUALVERIFY.  This will allow me also add another OP_EQUALVERIFY that should allow me to redeem this non-standard script with a standard P2PKH scriptsig. The code will look like the following.

```BASH
\\Original transaction
0200000001e42e10de7951a38e78688bce5512ba73f7601c06dea24c042c055d6f04efc8690100000000ffffffff01905f010000000000
17
a9 14 aed791c6a028b92d452df8711543f5d5d748d14d 87 
00000000

\\1+2=3
0101010293010388

\\Modified transaction
0200000001e42e10de7951a38e78688bce5512ba73f7601c06dea24c042c055d6f04efc8690100000000ffffffff01905f010000000000
21
76 a9 14 aed791c6a028b92d452df8711543f5d5d748d14d 88 01010102930103 88  ac
00000000
```

Once we have the bytecode modify, lets use decode and see what we came up with.

```BASH
> $ bc decoderawtransaction 0200000001e42e10de7951a38e78688bce5512ba73f7601c06dea24c042c055d6f04efc8690100000000ffffffff01905f0100000000002176a91405fc5a6bd7dce4ed2d5a6f9fca06b310d22c2976880101010293010388ac00000000
{
  "txid": "7f5b19afde7e314b787fdcbee669c55d6ca0ddcabef6f234103ee0e48d4bd616",
  "hash": "7f5b19afde7e314b787fdcbee669c55d6ca0ddcabef6f234103ee0e48d4bd616",
  "version": 2,
  "size": 93,
  "vsize": 93,
  "locktime": 0,
  "vin": [
    {
      "txid": "69c8ef046f5d052c044ca2de061c60f773ba1255ce8b68788ea35179de102ee4",
      "vout": 1,
      "scriptSig": {
        "asm": "",
        "hex": ""
      },
      "sequence": 4294967295
    }
  ],
  "vout": [
    {
      "value": 0.00090000,
      "n": 0,
      "scriptPubKey": {
        "asm": "OP_DUP OP_HASH160 05fc5a6bd7dce4ed2d5a6f9fca06b310d22c2976 OP_EQUALVERIFY 1 2 OP_ADD 3 OP_EQUALVERIFY OP_CHECKSIG",
        "hex": "76a91405fc5a6bd7dce4ed2d5a6f9fca06b310d22c2976880101010293010388ac",
        "type": "nonstandard"
      }
    }
  ]
}
```

We can see that this is indeed a valid scriptPubKey.  Go ahead sign and send this transaction and then look up the TXID on blockcypher.  You will notice that the output is displayed with 'Unknown script type'.  This means that we are outside of the standard bitcoin transaction types.  If we want to create these types of advanced scripts we will have to use what is known as P2SH.  However, even with P2SH, unless we use a "standard" script within P2SH the wallet will also not recognize the transaction.  This makes it quite difficult to deal with non-standard scripts without creating scripts to interpret the non-standard inputs and create non-standard outputs.

# Create a P2PKH wrapped P2SH transaction

# Tutorial on Advanced Scripting

The things that you can do with advanced scripting are quite extensive.  A very good set of tutorials are available on youtube at the following links.  If you are interested in learning more please go ahead and watch.

Advanced Bitcoin Scripting -- Part 1: Transactions & Multisig
https://www.youtube.com/watch?v=8FeAXjkmDcQ

Advanced Bitcoin Scripting -- Part 2: SegWit, Consensus, & Trustware
https://www.youtube.com/watch?v=pQbeBduVQ4I

# Interpretting advanced scripts

The following is an example of an advanced script.  Use btcdeb to decode the transaction.  You can then logically follow and answer the questions on what will happen under different conditions.

```BASH
btcdeb --txin=01000000015d9ce60782943c18a1ef1579d9bda773c80e0ab199a2c9b588cdfd482796b29e000000004847304402207553fb08bda01fb57d36dcc6c42336af50794972fdbd776e6715afcd5a8339910220710fb92d6be4ceb59a8449cb771df0b84f46d8f727bd86d0ea64a4290acbb46e01feffffff0240420f000000000017a914d5ea95de87cda1820542416554556653527721e687e8a0f629010000001976a914f8887547f9fa7a3c3495784880e9519ea990403288ac65000000 --tx=0100000001ab93dcd1f33ff0d34cf9086b143b1908aae9e99613d4293c5d287da3c9c606ab00000000eb48304502210080a8b1dcbf7d1335c57bf646619e2500233275b9b637e63f3cb36541491b5707022037b2469fc9446c061130641f181d138d2fd863c82fb42059c52728a6e3392b1a01207795c094909e85ccd1f4379a672295147bdbb8fdd6723b54e505def2145f395c512102a34d67615ab535bcbb75fde70c0fbfbf9c948eeb55df403fe0e7044dd55c5ed24c5c76a97263a820c922104042b24a97852bfe674f337558909cb2bcfc901e9d55ed93a1939567878814874f04b19a5b282fe136e01c882c088439b6a181670431de8257b16d14874f04b19a5b282fe136e01c882c088439b6a1816888ac000000000100400f00000000001976a914874f04b19a5b282fe136e01c882c088439b6a18188ac31de8257
```




