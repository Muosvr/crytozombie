# pragma and import
```solidity
pragma solidity ^0.4.19; <br />
import “./another_file.sol”;
```

# data types (uint, address, string, struct, mapping)
```solidity
uint num = 353;
string name = “Jack”;

uint[2] fixedArray;
string[5] stringArray;
uint[] dynamicArray

struct Person {
uint age;
string name;
}
Person satoshi = Person(172, “Satochi”);

mapping (string => uint) tokenID;
tokenID[“CrytoKitty”] = 12;

address CrytoKitty = 0x06012c8cf97BEaD5deAe237070F9587f8E7A266d;
```
# function
```solidity
Person [] voters;
function simple_function(string _name, uint _age) {
	voters.push(Person(_name, _age));
}

function multiply (uint a, uint b) return (uint) {
	return a * b;
}
```
Functions can have multiple returns

# contract
A contract is the building block of Ethereum applications similar to a class where the functions and variables inside belong to. In Solidity a contract once deployed will be given an address associated with it so other contract or dApp can call it.

```solidity
//Sample from Ethereum

contract MyToken {
    /* This creates an array with all balances */
    mapping (address => uint256) public balanceOf;

    /* Initializes contract with initial supply tokens to the creator of the contract */
    function MyToken(
        uint256 initialSupply
        ) public {
        balanceOf[msg.sender] = initialSupply;              // Give the creator all initial tokens
    }

    /* Send coins */
    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(balanceOf[msg.sender] >= _value);           // Check if the sender has enough
        require(balanceOf[_to] + _value >= balanceOf[_to]); // Check for overflows
        balanceOf[msg.sender] -= _value;                    // Subtract from the sender
        balanceOf[_to] += _value;                           // Add the same to the recipient
        return true;
    }
}
```


# Inheritance
Solidity contracts can inherit from other contracts(assuming the other contract is imported above). Contract inheritance allows a contract to access methods and variables in another contract similar to a class inheritance in another programming language.

```solidity
contract parentContract{
  function sayHello() public returns (string) {
    return "Hello!";
  }
}

// childContract inherits from parentContract and gains access to its sayHello() method
contract childContract is parentContract{
  function sayHi() public returns (string) {
    return "Hi!";
  }
}
```

# private, public, internal, external
Both variables and functions can be declared as public or private to indicate whether they are accessible outside of the contract. All functions are public by default which is not always desirable, it’s good practice to mark them all private unless needed to be called outside.
```solidity
//it is good practice to put an underscore in front of private function to indicate its hidden status from outside the contract
function _transferToken(uint amount, address from, address to) private {
//make the transfer
}
```
Internal is the same as private, except that it is also accessible to contracts that inherit from it. External is similar public except that these function can only be called outside of the contract.


# view, pure
If a function is declared as view or pure, they don’t cost any gas. But View function can only access data from the blockchain and not write, while pure functions only perform computation and does not access data or write data to the blockchain.

# require
A check can be placed on a function to check if criteria have been met before it runs, such as a payment has been made. 
```solidity
function approveTransfer(address accountHolder, uint tokenIndex) external {
require(accountHolder == msg.sender);
	//initiate transfer
}
```
# msg.sender
Gives the address of the party who calls the contract method.

# modifier
Similar to a function by is used to modify another function only. A modifier needs to be run before the function being modified is run. This can be used to reduce the amount of repetitive code. For example, instead of using require to verify sender identity in every function, you can use require in the modifier and use that modifier for every function that needs such verification.

The aforementioned pure, view are also modifiers
```solidity
//the underscore signals the end of the modifier so the program knows to return to the original function
modifier onlyOwner() {
	require(msg.sender == owner);
	_;
}

function transferOwnership(address newOwner) public onlyOwner {
	require(newOwner != address(0));
	OwnershipTransferred(owner, newOwner);
	owner = newOwner;
}
```
# payable modifier
Allows a function to handle the payment that comes with the function call message using message.value
```solidity
function purchaseProduct(uint productId, address buyer) external payable {
	require(msg.value == productFee);
	var newOwner = buyer;
	transferOwnership(uint productId, address newOwner);
}
```
# storage vs memory
Most of the time you don’t need to worry about this. Except when handling structs or arrays within functions. Storage is expensive because every time you write data, it is permanently added to the blockchain forever. Memory only exists until the end of the function call so it is much cheaper.
```solidity
//declaring  a memory array inside a function
uint[] memory values = new uint[](3)
```
For now, a memory array in Solidity needs to be declared with the length argument and cannot change size.

# Events
Events communicate what happens in a contract to the front end of your app that is listening. Use emit to send the event. You can also query past events using getPastEvents, which can be used as a cheaper form of storage. However, events are not readable from inside the smart contract.
```solidity
event IntegersAdded(uint x, uint y, uint result);

function add(uint x, uint y) public {
	uint result = x + y;
	emit IntegersAdded(x, y, result);
	return result;
}
```
# interface
Interacting with other contracts. You can create an interface inside your contract inorder to interact with another contract on the blockchain
```solidity
//code example
contract MyContract {
  address NumberInterfaceAddress = 0xab38... 
  // ^ The address of the FavoriteNumber contract on Ethereum
  NumberInterface numberContract = NumberInterface(NumberInterfaceAddress);
  // Now `numberContract` is pointing to the other contract

  function someFunction() public {
    // Now we can call `getNum` from that contract:
    uint num = numberContract.getNum(msg.sender);
    // ...and do something with `num` here
  }
}
```

# commenting your code
The Solidity community uses a format called natspec. Example below:
/// @title A contract for basic math operations
/// @author H4XF13LD MORRIS 💯💯😎💯💯
/// @notice For now, this contract just adds a multiply function
contract Math {
  /// @notice Multiplies 2 numbers together
  /// @param x the first uint.
  /// @param y the second uint.
  /// @return z the product of (x * y)
  /// @dev This function does not currently check for overflows
  function multiply(uint x, uint y) returns (uint z) {
    // This is just a normal comment, and won't get picked up by natspec
    z = x * y;
  }
}

# Deploying a contract
note: cryptocurrency tokens are also contracts themselves
https://medium.com/bitfwd/how-to-issue-your-own-token-on-ethereum-in-less-than-20-minutes-ac1f8f022793
https://www.ethereum.org/token

web3.js
handles communication and interaction between your front end to and the Ethereum blockchain. 

Installation optioins
using NPM
```bash
npm install web3
```
//using Yarn
yarn add web3

//Using Bower
bower install web3

or download the minified js file the link below and include in your html
 https://github.com/ethereum/web3.js/blob/1.0/dist/web3.min.js

web3 providers
Because Ethereum is made up of nodes, setting a web3 provider tells our web3.js which node to talk to communicate with the blockchain

Infura
Allows free API calls to access information in the blockchain, but cannot do write operations

Metamask
A secure way to let users manage their accounts as well as read and write into the Ethereum blockchain through a browser extension. Metamask will inject their web3 provider

Set up to detect Metamask present or prompt user to install:
window.addEventListener(‘load’, function() {
	if (typeof web3 !== ‘undefined’) {
		web3js = new Web3(web3.currentProvider);
	} else {
		// send users a message to install Metamask in order to use this app
}
//Now you can start your app & access web3js freely:
startApp();
} )

Talking to Contracts
ABI stands for Application Binary Interface, which is a JSON file that tells your web3.js how to interact with your contract. When you compile a contract, it will generate an ABI. You can also look up the ABI and address of any deployed contracts on Etherscan: https://etherscan.io/ 

//instantiate a contract
var myContract = new web3js.eth.Contract(myABI, myContractAddress);

Call
//Using call to access data on the contract
myContract.methods.aDifferentMethod().call()

//For version 1.0 of web3.js methods calls are handled using promises, older versions of web3.js might use callbacks
//suppose there is a function in myContract called accountDetials that shows account information
function getAccountDetails(id) {
	return myContract.methods.accountDetails(id).call()
}

getAccountDetails(20)
.then(function(result) {
	console.log(“Account info:”, JSON.stringify(result));
});
Send	
//using send to create a transaction and write to the blockchain. Send requires an address, which is accessible through message.sender in the contract.
myContract.methods.myMethod(123).send({from: address})

//example code from crypto zombies
function createRandomZombie(string _name) public {
  require(ownerZombieCount[msg.sender] == 0);
  uint randDna = _generateRandomDna(_name);
  randDna = randDna - randDna % 100;
  _createZombie(_name, randDna);
}

function createRandomZombie(name) {
  // This is going to take a while, so update the UI to let the user know
  // the transaction has been sent
  $("#txStatus").text("Creating new zombie on the blockchain. This may take a while...");
  // Send the tx to our contract:
  return cryptoZombies.methods.createRandomZombie(name)
  .send({ from: userAccount })
  .on("receipt", function(receipt) {
    $("#txStatus").text("Successfully created " + name + "!");
    // Transaction was accepted into the blockchain, let's redraw the UI
    getZombiesByOwner(userAccount).then(displayZombies);
  })
  .on("error", function(error) {
    // Do something to alert the user their transaction has failed
    $("#txStatus").text(error);
  });
}

Calling payable function

Wei
the smallest subunit of Ether, there are 10^18 wei in one ether. This is the amount to specify when using the send method
converting ether to wei with:
web3js.utils.toWei("1"); //convert 1ETH to Wei

cryptoZombies.methods.levelUp(zombieId)
.send({ from: userAccount, value: web3js.utils.toWei("0.001", "ether") })

listening(subscribing) for events
This code is triggered everytime the specified event is triggered in the contract

//code sample
Mycontract.events.myEvent()
.on("data", function(event) {
  let data = event.returnValues;
  // We can access this event's 3 return values on the `event.returnValues` object:
  console.log("Event values: ", data.value_name1, date.value_name2, data.value_name3);
}).on("error", console.error);