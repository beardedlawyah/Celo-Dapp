# Celo-Dapp: A simple DApp for a decentralized marketplace on Celo using Solidity and Truffle.

##  Introduction
In this tutorial, we will build a simple DApp for a decentralized marketplace on the Celo blockchain using Solidity and Truffle. We will cover the process of setting up the development environment, writing the smart contract, compiling and deploying the smart contract to the blockchain, and building a frontend to interact with the smart contract.

## Table of Contents

- [Celo-Dapp: A simple DApp for a decentralized marketplace on Celo using Solidity and Truffle](#celo-Dapp-A-simple-DApp-for-a-decentralized-marketplace-on-celo-using-solidity-and-truffle)
  - [Introduction](#introduction)
  - [Table of Contents](#table-of-contents)
  - [Objective](#objective)
  - [Prerequisites](#prerequisites)
  - [Requirements](#requirements)
  - [Setting Up the Development Environment](#setting-up-the-development-environment)
  - [Writing the Smart Contract](#Writing-the-Smart-Contract)
  - [Compiling and Deploying the Smart Contract](#compiling-and-deploying-the-smart-contract)
  - [Conclusion](#conclusion)

## Objective 
The objective of this tutorial is to provide a step-by-step guide on how to build a simple DApp for a decentralized marketplace on the Celo blockchain, using Solidity and Truffle. The tutorial assumes a good understanding of Solidity and basic command line proficiency. The tutorial will cover the process of setting up the development environment, writing the smart contract, compiling and deploying the smart contract to the blockchain, and building a frontend to interact with the smart contract. By the end of this tutorial, the reader will have a basic understanding of how to build and deploy a DApp on the Celo blockchain using Solidity and Truffle.


## Prerequisites
- Good understanding of Solidity
- Command line proficiency: Basic familiarity with using command line tools such as Terminal or Command Prompt is necessary for running commands and scripts.

## Requirements

- A text editor e.g. VSCode
- You will need to have node.js installed on your system, with version V10. or higher.
- npm (node package manager) used for installing and managing dependencies.


## Setting Up the Development Environment

Before we can start building our DApp, we need to set up our development environment. We will need the following:

- Node.js and NPM (Node Package Manager) - to install and manage our packages.
- Truffle:  a development framework for Ethereum and Celo.
- Ganache - a personal blockchain for development purposes.

``` 
node -v
npm -v
```

To install Truffle, run the following command:
```
npm install -g truffle
```
To install Ganache, head to the official website and download the appropriate version for your operating system. Once installed, open the application and start a new workspace.

To install Celo CLI, run the following command:
```
npm install -g @celo/cli
```
Now that our development environment is set up, we can create our project. In your terminal, create a new directory for your project and navigate into it:
```
mkdir celo-marketplace
cd celo-marketplace
```
We will use Truffle to create a new project:
```
truffle init
```
This command creates a new Truffle project with the basic structure for a DApp. The project has a contracts directory for Solidity smart contracts, a migrations directory for deploying the contracts to the blockchain, a test directory for testing the contracts, and a truffle-config.js file for configuring Truffle.

## Writing the Smart Contract

Our DApp will be a simple marketplace, where users can buy and sell items. Let's start by writing the smart contract. In your contracts directory, create a new file called Marketplace.sol:

``` solidity
pragma solidity ^0.8.0;

contract Marketplace {
    struct Item {
        string name;
        string description;
        uint256 price;
        bool sold;
        address payable seller;
        address payable buyer;
    }

    mapping(uint256 => Item) public items;
    uint256 public itemCount;

    event ItemAdded(uint256 indexed itemId, string name, string description, uint256 price, address indexed seller);
    event ItemSold(uint256 indexed itemId, address indexed seller, address indexed buyer);

    function addItem(string memory _name, string memory _description, uint256 _price) public {
        items[itemCount] = Item(_name, _description, _price, false, payable(msg.sender), address(0));
        emit ItemAdded(itemCount, _name, _description, _price, msg.sender);
        itemCount++;
    }

    function buyItem(uint256 _itemId) public payable {
        Item storage item = items[_itemId];
        require(!item.sold, "Item already sold");
        require(msg.value >= item.price, "Not enough funds");
        item.sold = true;
        item.buyer = payable(msg.sender);
        item.seller.transfer(item.price);
        emit ItemSold(_itemId, item.seller, msg.sender);
    }
}
```
In this smart contract, we define a struct called Item to represent a marketplace item. We use a mapping to store all the items and a counter to keep track of the number of items. We also define two events to emit when an item is added or sold.

## The addItem function
The addItem function takes in three parameters: the name, description, and price of the item. It creates a new Item struct and adds it to the items mapping. It then emits an ItemAdded event with the details of the item and the seller's address.

The buyItem function takes in the itemId of the item to buy. It checks that the item has not already been sold and that the buyer has sent enough funds to purchase the item. If the conditions are met, it marks the item as sold, sets the buyer's address, transfers the funds to the seller, and emits an ItemSold event with the details of the item, the seller's address, and the buyer's address.

## Compiling and Deploying the Smart Contract

Now that we have written our smart contract, we need to compile and deploy it to the blockchain. In your terminal, run the following command to compile the contract:
```
truffle compile
```
This command compiles the smart contract and generates the necessary bytecode and ABI files in the build/contracts directory.

Next, we need to deploy the contract to the blockchain. We will use Ganache as our local blockchain. In your terminal, run the following command to start Ganache:

```
ganache-cli
```
This command starts a local blockchain with 10 accounts and 100 ETH in each account.

We also need to configure Truffle to use Ganache as the network to deploy our contract to. In your truffle-config.js file, add the following code:
```
module.exports = {
  networks: {
    development: {
      host: "127.0.0.1",
      port: 8545,
      network_id: "*", // Match any network id
    },
  },
};
```
This code tells Truffle to use the development network with Ganache running on localhost and port 8545.

Now we can deploy our contract to the blockchain. In your terminal, run the following command:
```
truffle migrate
```
This command deploys the contract to the blockchain and generates a migration file in the migrations directory.

Interacting with the Smart Contract

We can now interact with our smart contract using the Celo CLI. In your terminal, connect to the Celo network by running the following command:
```
celocli network:switch --network alfajores
```
This command switches to the Alfajores network, which is a test network on the Celo blockchain.

To interact with the contract, we need its address. In your terminal, run the following command to get the address of the deployed contract:

```
truffle networks
```
This command lists the networks that we have deployed our contract to. Look for the development network and copy the address of the Marketplace contract.

Now we can use the Celo CLI to interact with the contract. In your terminal, run the following command to add an item to the marketplace:
```
celocli contract:call --to <Marketplace_address> --data "$(truffle exec scripts/add-item.js)"
```
Replace <Marketplace_address> with the address of the deployed Marketplace contract.

This command calls the addItem function of the contract with the name "Test Item", description "This is a test item", and price 1 CELO.

To buy an item, run the following command in your terminal:
``` 
celocli contract:call --to <Marketplace_address> --value 1 --data "$(truffle exec scripts/buy-item.js)"
```
Replace <Marketplace_address> with the address of the deployed Marketplace contract.

This command calls the buyItem function of the contract with the itemId 0 and sends 1 CELO to purchase the item.

## Conclusion

In this tutorial, we built a simple marketplace DApp on the Celo blockchain using Solidity and Truffle. We wrote a smart contract that allows sellers to add items to the marketplace and buyers to purchase those items using CELO.

We then compiled and deployed the contract to a local blockchain using Truffle and Ganache. Finally, we interacted with the contract using the Celo CLI to add an item to the marketplace and buy that item.

This tutorial provides a basic example of how to build a DApp on the Celo blockchain. You can extend this example to include additional functionality, such as allowing sellers to remove items from the marketplace or allowing buyers to leave feedback for sellers.

The Celo blockchain provides a secure and efficient platform for building decentralized applications. Its focus on mobile accessibility and ease of use makes it an ideal platform for building DApps that can reach a wide audience. With the tools and resources available, developers can create powerful and innovative applications that can revolutionize the way we interact with the world.

