---
layout: post
title:  "UseCase_Block"
permalink: /lessons/introduction/
---


# Listening to new blocks

- ##### Get notified when a new block is included.

# Prerequisites
- ##### Finish the getting started section
- ##### Text editor or IDE
- ##### NEM2-SDK or CLI

# Let’s get into some code

```javascript
const listener = new Listener('120.79.181.170:3000');

listener.open().then(() => {

    listener
        .newBlock()
        .subscribe(block => console.log(block), err => console.error(err));

});
```


# Getting block by height
- ##### Get the block information given a height.

# Prerequisites
- ##### Finish the getting started section
- ##### Text editor or IDE
- ##### NEM2-SDK or CLI

# Let’s get into some code
- ##### Are you curious to see what happened in the genesis block?

```javascript

const blockchainHttp = new BlockchainHttp('120.79.181.170:3000');

const height = 1;

blockchainHttp
    .getBlockByHeight(height)
    .subscribe(block => console.log(block), err => console.error(err));
``` 
    
    
- ##### The following snippet returns the height of the latest block.

```javascript
const blockchainHttp = new BlockchainHttp('120.79.181.170:3000');

blockchainHttp
    .getBlockchainHeight()
    .subscribe(height => console.log(height.compact()), err => console.error(err));
```