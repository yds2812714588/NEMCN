---
layout: post
title:  "Usecase_Mintor"
permalink: /lessons/Usecase_Mintor/
---



# Monitor

- Language:TypeScript and JavaScript SDK 
- SDK-Version:v0.10.1-beta 
- MODEL:Monitor

## Prerequisites
- Finish the getting started section
- Text editor or IDE
- NEM2-SDK or CLI
- Description:you will receive a response if the change has been applied or failed due to some constraint.


## **1. Aggregatebonded **

- Description: Monitor aggregate bonded transactions added
- Code sample:

```javascript
 /**
     * Return an observable of {@link AggregateTransaction} for specific address.
     * Each time an aggregate bonded transaction is announced,
     * it emits a new {@link AggregateTransaction} in the event stream.
     *
     * @param address address we listen when a transaction with missing signatures state
     * @return an observable stream of AggregateTransaction with missing signatures state
     */
    public aggregateBondedAdded(address: Address): Observable<AggregateTransaction> {
        this.subscribeTo(`partialAdded/${address.plain()}`);
        return this.messageSubject.asObservable().pipe(
            filter((_) => _.channelName === ListenerChannelName.aggregateBondedAdded),
            filter((_) => _.message instanceof AggregateTransaction),
            map((_) => _.message as AggregateTransaction),
            filter((_) => this.transactionFromAddress(_, address)),);
    }

```
### **How to use**

```
describe('TransactionHttp', () => {
    let account;
    let account2;
    let transactionHttp;
    let accountHttp;
    let namespaceName;
    let mosaicId;
    let listener;
    const transactionHash = 'A192621335D733351A7035644F87338B0B9E36B3FAE61253E230B1A0D8BEA332';
    const transactionId = '5A2139FC9CD1E80001573357';
    before(() => {
        account = conf_spec_1.TestingAccount;
        account2 = conf_spec_1.CosignatoryAccount;
        transactionHttp = new TransactionHttp_1.TransactionHttp(conf_spec_1.APIUrl);
        accountHttp = new AccountHttp_1.AccountHttp(conf.APIUrl);
        listener = new Listener_1.Listener(conf_spec_1.APIUrl);
        return listener.open();
    });
     
    it('aggregateBondedTransactionsAdded', (done) => {
        listener.aggregateBondedAdded(multisigAccount.address)
            .toPromise()
            .then((res) => {
                done();
            });
        setTimeout(() => {
            TransactionUtils_1.TransactionUtils.createAggregateBoundedTransactionAndAnnounce();
        }, 1000);
    });        

```


## 2.Block           


- Description : Monitor new blocks   

-  Code sample 

```javascript
   /**
     * Returns an observable stream of BlockInfo.
     * Each time a new Block is added into the blockchain,
     * it emits a new BlockInfo in the event stream.
     *
     * @return an observable stream of BlockInfo
     */
 public newBlock(): Observable<BlockInfo> {
        this.subscribeTo('block');
        return this.messageSubject
            .asObservable().pipe(
            share(),
            filter((_) => _.channelName === ListenerChannelName.block),
            filter((_) => _.message instanceof BlockInfo),
            map((_) => _.message as BlockInfo),);
    }

```

### **How to use**


```javascript

describe('TransactionHttp', () => {
    let account;
    let account2;
    let transactionHttp;
    let accountHttp;
    let namespaceName;
    let mosaicId;
    let listener;
    const transactionHash = 'A192621335D733351A7035644F87338B0B9E36B3FAE61253E230B1A0D8BEA332';
    const transactionId = '5A2139FC9CD1E80001573357';
    before(() => {
        account = conf_spec_1.TestingAccount;
        account2 = conf_spec_1.CosignatoryAccount;
        transactionHttp = new TransactionHttp_1.TransactionHttp(conf_spec_1.APIUrl);
        accountHttp = new AccountHttp_1.AccountHttp(conf.APIUrl);
        listener = new Listener_1.Listener(conf_spec_1.APIUrl);
        return listener.open();
    });
    
    
 it('newBlock', (done) => {
        listener.newBlock()
            .toPromise()
            .then((res) => {
                done();
            });

        TransactionUtils.createAndAnnounce();
    });

```
## 3.Confirmed  

- Description : Monitor confirmed transactions added  
- Code sample 

```javascript
  /**
     * Returns an observable stream of Transaction for a specific address.
     * Each time a transaction is in confirmed state an it involves the address,
     * it emits a new Transaction in the event stream.
     *
     * @param address address we listen when a transaction is in confirmed state
     * @return an observable stream of Transaction with state confirmed
     */
    public confirmed(address: Address): Observable<Transaction> {
        this.subscribeTo(`confirmedAdded/${address.plain()}`);
        return this.messageSubject.asObservable().pipe(
            filter((_) => _.channelName === ListenerChannelName.confirmedAdded),
            filter((_) => _.message instanceof Transaction),
            map((_) => _.message as Transaction),
            filter((_) => this.transactionFromAddress(_, address)),);
    }

```
### **How to use**
```


describe('Listener', () => {
    let account;
    let multisigAccount;
    let listener: Listener;

    before(() => {
        account = TestingAccount;
        multisigAccount = MultisigAccount;
        listener = new Listener(APIUrl);
        return listener.open();
    });

    after(() => {
        listener.close();
    });



 it('confirmedTransactionsGiven address signer', (done) => {
        listener.confirmed(account.address)
            .toPromise()
            .then((res) => {
                done();
            });

        TransactionUtils.createAndAnnounce();
    });
    
    
    }
        
```


## 4.Cosignature     
- Description: Monitor cosignatures added  
- Code sample 

```javascript
  /**
     * Returns an observable stream of {@link CosignatureSignedTransaction} for specific address.
     * Each time a cosigner signs a transaction the address initialized,
     * it emits a new message with the cosignatory signed transaction in the even stream.
     *
     * @param address address we listen when a cosignatory is added to some transaction address sent
     * @return an observable stream of {@link CosignatureSignedTransaction}
     */
    public cosignatureAdded(address: Address): Observable<CosignatureSignedTransaction> {
        this.subscribeTo(`cosignature/${address.plain()}`);
        return this.messageSubject.asObservable().pipe(
            filter((_) => _.channelName === ListenerChannelName.cosignature),
            filter((_) => _.message instanceof CosignatureSignedTransaction),
            map((_) => _.message as CosignatureSignedTransaction),);
    }
       
```       

### **How to use**
```

describe('Listener', () => {
    let account;
    let multisigAccount;
    let listener: Listener;

    before(() => {
        account = TestingAccount;
        multisigAccount = MultisigAccount;
        listener = new Listener(APIUrl);
        return listener.open();
    });

    after(() => {
        listener.close();
    });



 it('confirmedTransactionsGiven address signer', (done) => {
 it('cosignatureAdded', (done) => {
        listener.cosignatureAdded(multisigAccount.address)
            .toPromise()
            .then((res) => {
                done();
            });
    
    
    }
    
    
 

```
        
## 5.Status          

- Description:- Monitor transaction status error    
- Code sample:

```javascript
   /**
     * Returns an observable stream of {@link TransactionStatusError} for specific address.
     * Each time a transaction contains an error,
     * it emits a new message with the transaction status error in the event stream.
     *
     * @param address address we listen to be notified when some error happened
     * @return an observable stream of {@link TransactionStatusError}
     */
    public status(address: Address): Observable<TransactionStatusError> {
        this.subscribeTo(`status/${address.plain()}`);
        return this.messageSubject.asObservable().pipe(
            filter((_) => _.channelName === ListenerChannelName.status),
            filter((_) => _.message instanceof TransactionStatusError),
            map((_) => _.message as TransactionStatusError),);
    }

```
### **How to use** 
```

describe('Listener', () => {
    let account;
    let multisigAccount;
    let listener: Listener;

    before(() => {
        account = TestingAccount;
        multisigAccount = MultisigAccount;
        listener = new Listener(APIUrl);
        return listener.open();
    });

    after(() => {
        listener.close();
    });



 it('confirmedTransactionsGiven address signer', (done) => {
 it('cosignatureAdded', (done) => {
 
        it('transactionStatusGiven', (done) => {
        listener.status(account.address)
            .toPromise()
            .then((res) => {
                done();
            });
    
    }

```


## 6.Unconfirmed    

- Description: - Monitor unconfirmed transactions added 

- Code sample 

```javascript

    /**
     * Returns an observable stream of Transaction for a specific address.
     * Each time a transaction is in unconfirmed state an it involves the address,
     * it emits a new Transaction in the event stream.
     *
     * @param address address we listen when a transaction is in unconfirmed state
     * @return an observable stream of Transaction with state unconfirmed
     */
    public unconfirmedAdded(address: Address): Observable<Transaction> {
        this.subscribeTo(`unconfirmedAdded/${address.plain()}`);
        return this.messageSubject.asObservable().pipe(
            filter((_) => _.channelName === ListenerChannelName.unconfirmedAdded),
            filter((_) => _.message instanceof Transaction),
            map((_) => _.message as Transaction),
            filter((_) => this.transactionFromAddress(_, address)),);
    }
    

```

### **How to use** 
```


describe('Listener', () => {
    let account;
    let multisigAccount;
    let listener: Listener;

    before(() => {
        account = TestingAccount;
        multisigAccount = MultisigAccount;
        listener = new Listener(APIUrl);
        return listener.open();
    });

    after(() => {
        listener.close();
    });



 it('unconfirmedTransactionsAdded', (done) => {
        listener.unconfirmedAdded(account.address)
            .toPromise()
            .then((res) => {
                done();
            });
            
            
}    
     
```








