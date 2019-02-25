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

### Debugging Errors in btcdeb










Before we begin writing our own bitcoin scripts lets look at a couple of examples that exist by using blockcypher.  The first thing that we will look at are P2PKH scripts which are the most common type of script used in bitcoin today.  This will make you dig into the component pieces of a transaction more and give you your first example of a successfully running bitcoin transaction script.

https://api.blockcypher.com/v1/btc/test3/txs/7a5f71cdc1e4f877f695ea35d7e1bee75283571544014f16f177f8d01155dd9e?includeHex=true

{
  "block_hash": "0000000000000082ebf181715da77d4883f85be010ca8151b294c942a5c3f1ec",
  "block_height": 1454213,
  "block_index": 8,
  "hash": "7a5f71cdc1e4f877f695ea35d7e1bee75283571544014f16f177f8d01155dd9e",
  "hex": "0200000000010108937990081f74b10ce7ae79065f1ecd2dbbf9710941cc44e450f7a17951311b01000000171600148c20a264b1c162f388fe0e9992e2e87d1830adefffffffff01ecad8500000000001976a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac024830450221009f9fb0c5b826b0a869e51c0318bc459e40e748a917fc2e13e07cea3a1dcc75b502200dcd4a1c4bc6173c2ec8127246586bb5456c0cf2196f2012d907d744d74f0015012103661b91afaa5ddcdecc10f4be5e282427f6f00672cb06293e802eeccd9364219400000000",
  "addresses": [
    "2Mu8ciAruGtBrBCQVRqh5SsZBk3G2Nh7Ns9",
    "mywBT1RBVqhzCTQpc3pycbJkM7v1tagQdA"
  ],
  "total": 8760812,
  "fees": 10000,
  "size": 108,
  "preference": "high",
  "relayed_by": "173.0.157.36:18333",
  "confirmed": "2019-01-28T07:18:41Z",
  "received": "2019-01-28T07:16:58.928Z",
  "ver": 2,
  "double_spend": false,
  "vin_sz": 1,
  "vout_sz": 1,
  "confirmations": 27036,
  "confidence": 1,
  "inputs": [
    {
      "prev_hash": "1b315179a1f750e444cc410971f9bb2dcd1e5f0679aee70cb1741f0890799308",
      "output_index": 1,
      "script": "1600148c20a264b1c162f388fe0e9992e2e87d1830adef",
      "output_value": 8770812,
      "sequence": 4294967295,
      "addresses": [
        "2Mu8ciAruGtBrBCQVRqh5SsZBk3G2Nh7Ns9"
      ],
      "script_type": "pay-to-script-hash",
      "age": 1454160,
      "witness": [
        "30450221009f9fb0c5b826b0a869e51c0318bc459e40e748a917fc2e13e07cea3a1dcc75b502200dcd4a1c4bc6173c2ec8127246586bb5456c0cf2196f2012d907d744d74f001501",
        "03661b91afaa5ddcdecc10f4be5e282427f6f00672cb06293e802eeccd93642194"
      ]
    }
  ],
  "outputs": [
    {
      "value": 8760812,
      "script": "76a914ca073a588b2ea809fcfe4aae9f89fa73acf4117788ac",
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


3045022100ac7169d5d1359082ca820903d21c969d27d6e9cc8e5adee8a64ecfe90aa7d19f0220131c46e31b36297f94b1163693c55352d0fe457a3b9740b496fd23829a827cbb[ALL] 038e21b7eb62fa4412c195c6c5db3ce37943e5b4f77d2ef0a07b32bddcb7fe7ab0

The 

Tools
https://github.com/kallewoof/btcdeb

Introduction
Examples
Pay-to-Pubkey-Hash
m-of-n Multi-Signature Scripts
Escrow Account
Payment Channels
