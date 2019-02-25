---
layout: post
title:  "UseCase_Namspace"
permalink: /lessons/UseCase_Namspace/
---


# Registering a namespace
##### Rregister your own namespace.

# Background
##### A namespace is an on-chain unique domain for your assets. The easiest way to understand it is by means of the domain-file analogy on the internet.

##### A mosaic is like a file hosted on a domain and represents an asset. Like a website and directory, a mosaic can have the same name as other files on other domains. However, a namespace + mosaic is always unique.

##### If an account creates a namespace, that namespace will appear as unique in the NEM ecosystem. For example, if one were to create a namespace called foo, a second person cannot create the same namespace.

# Prerequisites

- ##### Finish the getting started section
- ##### NEM2-SDK or CLI
- ##### A text editor or IDE
- ##### An account with XEM


# Let’s get into some code

##### Register your namespace by choosing a name you like. One common option is to use your company’s or own name. In this example, we will register a namespace called foo.

##### 1.Check if this nampespace name is available.

```javascript
const namespaceHttp = new NamespaceHttp('120.79.181.170:3000');

const namespace = new NamespaceId('foo');

namespaceHttp
    .getNamespace(namespace)
    .subscribe(namespace => console.log(namespace), err => console.error(err));
```


##### 2.Is the namespace available? Try to register it before someone else does it! Announce a register namespace transaction with the chosen name and renting duration expressed in blocks.

##### **Note**
##### In Catapult, NEM blocks are complete every 15 seconds in average.

```javascript
const transactionHttp = new TransactionHttp('120.79.181.170:3000');

const privateKey = process.env.PRIVATE_KEY as string;
const account = Account.createFromPrivateKey(privateKey, NetworkType.MIJIN_TEST);

const namespaceName = "foo"; //Replace with an unique namespace name

const registerNamespaceTransaction = RegisterNamespaceTransaction.createRootNamespace(
    Deadline.create(),
    namespaceName,
    UInt64.fromUint(1000),
    NetworkType.MIJIN_TEST);

const signedTransaction = account.sign(registerNamespaceTransaction);

transactionHttp
    .announce(signedTransaction)
    .subscribe(x => console.log(x), err => console.error(err));
```




# Registering a subnamespace
##### Register a subnamespace following this guide.

# Background
##### Once you have a registered root namespace, you can create up to 3 levels of subnamespaces.

##### Subnamespaces do not have a renting duration on its own. They have the same one as their parent namespace.

##### It is possible to create multiple subnamespaces with the same name in different namespaces (example: foo.bar and foo2.bar).

# Prerequisites
- ##### Finish registering a namespace guide
- ##### NEM2-SDK or CLI
- ##### A text editor or IDE
- ##### An account with XEM and at least one namespace


# Let’s get into some code
##### The first step is to choose a name for your subnamespace.

##### In this example, we have registered a subnamespace called bar under foo namespace.

```javascript
const transactionHttp = new TransactionHttp('120.79.181.170:3000');

const privateKey = process.env.PRIVATE_KEY as string;
const account = Account.createFromPrivateKey(privateKey, NetworkType.MIJIN_TEST);

const rootNamespaceName = 'foo';
const subnamespaceName = 'bar';

const registerNamespaceTransaction = RegisterNamespaceTransaction.createSubNamespace(
    Deadline.create(),
    subnamespaceName,
    rootNamespaceName,
    NetworkType.MIJIN_TEST);

const signedTransaction = account.sign(registerNamespaceTransaction);

transactionHttp
    .announce(signedTransaction)
    .subscribe(x => console.log(x), err => console.error(err));
```