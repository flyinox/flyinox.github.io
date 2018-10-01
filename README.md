# blockly-chaincode

## Description

This is the graphic visual programming editor for designing chaincode of Hyperledger Fabric.

## Purpose

blockly-chaincode is based on Blockly, the web-based, graphical programming editor. It provides specific blocks and code generators for chaincode design. You can simply drag blocks together to design your own chaincode.

## Motivations

Chaincode (as we called smart contract) typically handles business logic agreed to by members of the network. Participants can reach consensus on process and data consistency of chaincode. As we might expect, chaincode over blockchain technology can apply in many fields, such as asset tokenization, supply chain management, digital identity, healcare and so on.

Ideally, chaincodes should be designed and read by domain experts, and even more, be understood by the public that get involed in it. But the truth is chaincodes are written by developers using specialized programming languages such as go, java or nodejs which other people can not take advantage of. Domain experts and other participants can not understand what the chaincode really mean by those specialized code. In this sense, the process flow of chaincode did not reach consensus on each part of the blockchain network.

blockly-chaincode is providing designability and readability for domain experts and other contract participants who can not perform code work.

On the other hand, blockly-chaincode is also providing efficient and fast way to design chaincode for professional chaincode developers. Just drag some blocks together, and you will get what you want. Besides that, if you want to design more complicated chaincode that blockly-chaincode can't cover full requirement for the moment, you can also design by it preliminarily and take the next step through the generated code from blocks. Or you just using it to validate and demostrate the ideas before you start coding.

Summarize above, blockly-chaincode can provide:

- ability for domain experts or other participants to design chaincodes
- more efficient and fast way for developers to design chaincodes in production environments
- rapid prototyping
- demonstrate or cooperate chaincode with some one through legible way

## Features

- generate Hyperledger Fabric chaincode (current support nodejs version)
- support for instantiate and invoke of chaincode. (different methods can be specified for invoke)
- chaincode api support like putState, getState, deleteState, shim.success. shim.error. (The number of api is growing)
- method scope variable: parameter variable (for keeping parameter passed in), local variable (for tempory use), getState variable (for keeping the state)
- security check, including parameter number check, parameter type check, try/catch wrapper
- full original blocks support including logic, math, list (except variable and function)
- object blocks support (create and get object, set and get property)
- graphic chaincode (based on xml) save and load
- support for I18N (now support English and simplified Chinese)

## Example

This is a blockly-chaincode example refers to example02 from fabric chaincode test.

![img](https://lh5.googleusercontent.com/gWbhmxLBGHPfsVASVOPz9qkrwKjbbAtcVPCwEP5RXTiWr7-puUd3LRvpb97RCJboH5gnd5hq_kjmyhtAI_wVlzDa_68r1KZ8B45wJojSFPn_xqQxXCJl7Ir8b2msC_fmOR1FXPyZ)

and it will generate the following nodejs code:


`````javascript
const shim = require('fabric-shim');

var logger = shim.newLogger('example02');

logger.level = 'info';



var Chaincode = class {

   async Init(stub) {

       let args = stub.getStringArgs();

       if (args.length !== 4){

           return shim.error('init expects 4 args');

       }

       let A = args.shift();

       let Aval = parseFloat(args.shift());

       if (isNaN(Aval)) {

           return shim.error('Invalid amount, expecting a float value');

       }

       let B = args.shift();

       let Bval = parseFloat(args.shift());

       if (isNaN(Bval)) {

           return shim.error('Invalid amount, expecting a float value');

       }

       await stub.putState(String(A), Buffer.from(JSON.stringify(Aval)));

       await stub.putState(String(B), Buffer.from(JSON.stringify(Bval)));

       return shim.success();

   }

   async Invoke(stub) {

       let ret = stub.getFunctionAndParameters();

       let fcn = ret.fcn;

       let args = ret.params;



       try{

           if(fcn === 'invoke'){

               let ret = await this.invoke(stub,args);

               return ret;

           }

       }catch(e){

           return shim.error(e)

       }

       return shim.error('Unknown action, check the first argument');

   }

   async invoke(stub,args){

       if (args.length !== 3){

           return shim.error('expects 3 args, but get ' + args.length + ' args');

       }

       let A = args.shift();

       let B = args.shift();

       let X = parseFloat(args.shift());

       if (isNaN(X)) {

           return shim.error('Invalid amount, expecting a float value');

       }

       if (!(await stub.getState(String(A))).byteLength != 0?true:false){

         return shim.error('Entity not found');

       }

       if (!(await stub.getState(String(B))).byteLength != 0?true:false){

         return shim.error('Entity not found');

       }

       let __Aval_bytes__ = await stub.getState(String(A));

       if(__Aval_bytes__.byteLength == 0){

           return shim.error('failed to getstate Aval');

       }

       let Aval = JSON.parse(__Aval_bytes__.toString());

       let __Bval_bytes__ = await stub.getState(String(B));

       if(__Bval_bytes__.byteLength == 0){

           return shim.error('failed to getstate Bval');

       }

       let Bval = JSON.parse(__Bval_bytes__.toString());

       await stub.putState(String(A), Buffer.from(JSON.stringify(Aval + X)));

       await stub.putState(String(B), Buffer.from(JSON.stringify(Bval + X)));

       return shim.success();

   }

}

shim.start(new Chaincode());
`````







