---
layout: post
title:  "Usecase_Mosaic"
permalink: /lessons/Usecase_Mosaic/
---




# Creating a mosaic
##### After creating a namespace, follow this guide to create a mosaic .

# Background
##### Mosaics can be used to represent any asset in the blockchain such as objects, tickets, coupons, stock share representation, and even your cryptocurrency.

##### A mosaic is like a file hosted on a domain and it represents an asset. Like a website and directory, a mosaic can have the same name as other files on other domains. However, a namespace + mosaic is always unique.

# Prerequisites
- ##### Finish registering a namespace guide
- ##### NEM2-SDK or CLI
- ##### A text editor or IDE
- ##### An account with XEM and at least one namespace

# Let’s get into some code
##### The first step is to choose a name for your mosaic. The name of the mosaic, up to a size limit of 64 characters, must be unique under the domain name.

##### Our mosaic will be called token, and its parent namespace will be foo.

```javascript
const transactionHttp = new TransactionHttp('120.79.181.170:3000');

const privateKey = process.env.PRIVATE_KEY as string;
const account = Account.createFromPrivateKey(privateKey, NetworkType.MIJIN_TEST);

// Replace with namespace name and mosaic name
const namespaceName = 'foo';
const mosaicName = 'token';
```

##### It is necessary to announce two transactions when creating a mosaic:

##### 1.A mosaic definition transaction, to create the mosaic.

##### Under mosaic properties, we define a mosaic with supplyMutable, transferable among accounts other than the creator and registered for 1000 blocks. foo:token won’t be divisible.

```javascript
const mosaicDefinitionTransaction = MosaicDefinitionTransaction.create(
    Deadline.create(),
    mosaicName,
    namespaceName,
    MosaicProperties.create({
        supplyMutable: true,
        transferable: true,
        levyMutable: false,
        divisibility: 0,
        duration: UInt64.fromUint(1000)
    }),
    NetworkType.MIJIN_TEST);
```

##### 2.A mosaic supply change transaction, to set the supply. foo:token initial supply is 1.000.000

##### **Note**
##### Once you announce a MosaicSupplyChangeTransaction, you cannot change mosaic properties for this mosaic.

```javascript
const mosaicSupplyChangeTransaction = MosaicSupplyChangeTransaction.create(
    Deadline.create(),
    mosaicDefinitionTransaction.mosaicId,
    MosaicSupplyType.Increase,
    UInt64.fromUint(1000000),
    NetworkType.MIJIN_TEST);
```

##### 3.Both transactions can be announced together using an aggregate transaction.

```javascript
const aggregateTransaction = AggregateTransaction.createComplete(
    Deadline.create(),
    [
        mosaicDefinitionTransaction.toAggregate(account.publicAccount),
        mosaicSupplyChangeTransaction.toAggregate(account.publicAccount)
    ],
    NetworkType.MIJIN_TEST,
    []);

const signedTransaction = account.sign(aggregateTransaction);

transactionHttp
    .announce(signedTransaction)
    .subscribe(x=> console.log(x),err => console.error(err));
```



# Modifying mosaic supply
##### Did you register a mosaic with supplyMutable option set to true? In that case, you can increase or decrease your mosaic available supply following this guide.

# Prerequisites
- ##### Finish creating a mosaic guide
- ##### NEM2-SDK or CLI
- ##### A text editor or IDE
- ##### An account with XEM
- ##### Have registered a supply mutable mosaic


# Let’s get into some code

##### If you have followed the previous guide, right now you should own a supply mutable mosaic.

##### Increase to 2.000.000 the initial supply. Sign and announce a mosaic supply change transaction.

```javascript
const transactionHttp = new TransactionHttp('120.79.181.170:3000');

const privateKey = process.env.PRIVATE_KEY as string;
const account = Account.createFromPrivateKey(privateKey, NetworkType.MIJIN_TEST);

const mosaicID = new MosaicId('foo:token');

const mosaicSupplyChangeTransaction = MosaicSupplyChangeTransaction.create(
    Deadline.create(),
    mosaicID,
    MosaicSupplyType.Increase,
    UInt64.fromUint(2000000),
    NetworkType.MIJIN_TEST);

const signedTransaction = account.sign(mosaicSupplyChangeTransaction);

transactionHttp
    .announce(signedTransaction)
    .subscribe(x=> console.log(x), err => console.error(err));
```