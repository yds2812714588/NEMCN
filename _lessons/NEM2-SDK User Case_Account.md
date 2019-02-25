---
layout: post
title:  "Usecase_Account"
permalink: /lessons/Usecase_Account/
---



# ACCOUNT

- Language:TypeScript and JavaScript SDK 
- SDK-Version:v0.10.1-beta 
- MODEL:ACOUNT

## Prerequisites
- Finish the getting started section
- Text editor or IDE
- NEM2-SDK or CLI
- Description:NIS supports two different kind of accounts: normal accounts and multsig (short for: multi signature) accounts.



## **1. 多重签名 aggregatebonded


- Description : Fetch aggregate bonded transactions from account.  An **aggregate transaction** is**bonded** when it requires signatures from other participants. When sending an **aggregate bonded transaction**, an account must first announce and get confirmed a hash lock **transaction** for this **aggregate**with at least 10 XEM.
-  Code sample 

```javascript
/**
* Gets an array of transactions for which an account is the sender or has sign the transaction.
* A transaction is said to be aggregate bonded with respect to an account if there are missing signatures.
* @param publicAccount - User public account
* @param queryParams - (Optional) Query params
* @returns Observable<AggregateTransaction[]>
*/
public aggregateBondedTransactions(publicAccount: PublicAccount,
queryParams?: QueryParams): Observable<AggregateTransaction[]> {

return observableFrom(
this.accountRoutesApi.partialTransactions(publicAccount.publicKey, queryParams != null ? queryParams : {})).pipe(
map((transactionsDTO) => {
return transactionsDTO.map((transactionDTO) => {
return CreateTransactionFromDTO(transactionDTO) as AggregateTransaction;
});
}));
}

```

### **How to use**

run javascript&javascript code

```javascript
const accountAddress = Address.createFromRawAddress('SDRDGFTDLLCB67D4HPGIMIHPNSRYRJRT7DOBGWZY');
const accountPublicKey = '1026D70E1954775749C6811084D6450A3184D977383F0E4282CD47118AF37755';
const publicAccount = PublicAccount.createFromPublicKey('846B4439154579A5903B1459C9CF69CB8153F6D0110A7A0ED61DE29AE4810BF2',
    NetworkType.MIJIN_TEST);
const multisigPublicAccount = PublicAccount.createFromPublicKey('B694186EE4AB0558CA4AFCFDD43B42114AE71094F5A1FC4A913FE9971CACD21D',
    NetworkType.MIJIN_TEST);

const accountHttp = new AccountHttp(APIUrl);



accountHttp
    .aggregateBondedTransactions(account.publicAccount)
    .pipe(
        mergeMap((_) => _),
        filter((_) => !_.signedByAccount(account.publicAccount)),
        map(transaction => cosignAggregateBondedTransaction(transaction, account)),
        mergeMap(cosignatureSignedTransaction => transactionHttp.announceAggregateBondedCosignature(cosignatureSignedTransaction))
    )
    .subscribe(announcedTransaction => console.log(announcedTransaction),
        err => console.error(err));
```

## 2. Generate account

- Description: Generate a new account address from the SDK
- Code sample:

```javascript
/**Create an Account from a given private key
**@param privateKey - Private key from an account
**@param networkType - Network type
**@return {Account}
*/

public static generateNewAccount(networkType: NetworkType): Account {
// Create random bytes
const randomBytesArray = nacl_catapult.randomBytes(32);
// Hash random bytes with entropy seed
// Finalize and keep only 32 bytes
const hashKey = convert.uint8ToHex(randomBytesArray); // TODO: derive private key correctly

// Create KeyPair from hash key
const keyPair = KeyPair.createKeyPairFromPrivateKeyString(hashKey);

const address = Address.createFromPublicKey(convert.uint8ToHex(keyPair.publicKey), networkType);
return new Account(address, keyPair);
}
```
### **How to use **

Run javascript&javascript code

```
const account = Account.generateNewAccount(NetworkType.MIJIN_TEST);

console.log('Your new account address is:', account.address.pretty(), 'and its private key', account.privateKey);
```
Bash code:

```
nem2-cli account generate
```



## 3.获取账户信息 getaccountinfo

- Description : Fetch account info  获取账户信息  
- Code sample 

```javascript
   /**
     * Gets an AccountInfo for an account.
     * @param address Address
     * @returns Observable<AccountInfo>
     */
    public getAccountInfo(address: Address): Observable<AccountInfo> {
        return observableFrom(this.accountRoutesApi.getAccountInfo(address.plain())).pipe(map((accountInfoDTO) => {
            return new AccountInfo(
                accountInfoDTO.meta,
                Address.createFromEncoded(accountInfoDTO.account.address),
                new UInt64(accountInfoDTO.account.addressHeight),
                accountInfoDTO.account.publicKey,
                new UInt64(accountInfoDTO.account.publicKeyHeight),
                accountInfoDTO.account.mosaics.map((mosaicDTO) => new Mosaic(
                    new MosaicId(mosaicDTO.id),
                    new UInt64(mosaicDTO.amount),
                )),
                new UInt64(accountInfoDTO.account.importance),
                new UInt64(accountInfoDTO.account.importanceHeight),
            );
        }));
    }
```
### **How to use**
```
const accountAddress = Address.createFromRawAddress('SDRDGFTDLLCB67D4HPGIMIHPNSRYRJRT7DOBGWZY');
const accountPublicKey = '1026D70E1954775749C6811084D6450A3184D977383F0E4282CD47118AF37755';
const publicAccount = PublicAccount.createFromPublicKey('846B4439154579A5903B1459C9CF69CB8153F6D0110A7A0ED61DE29AE4810BF2',
        NetworkType.MIJIN_TEST);
const multisigPublicAccount = PublicAccount.createFromPublicKey('B694186EE4AB0558CA4AFCFDD43B42114AE71094F5A1FC4A913FE9971CACD21D',
        NetworkType.MIJIN_TEST);
const accountHttp = new AccountHttp(APIUrl);

    describe('getAccountInfo', () => {
        it('should return account data given a NEM Address', (done) => {
            accountHttp.getAccountInfo(accountAddress)
                .subscribe((accountInfo) => {
                    expect(accountInfo.publicKey).to.be.equal(accountPublicKey);
                    done();
                });
        });
    });    
```

## 4.交易的接收者
- Description:Fetch incoming transactions from account 
   如果帐户是交易的接收者，则称交易相对于帐户进入。   
- Class:TransactionRepository.ts  
- Code sample 

```javascript
  /**
     * Gets an array of transactions for which an account is the recipient of a transaction.
     * A transaction is said to be incoming with respect to an account if the account is the recipient of a transaction.
     * @param publicAccount - User public account
     * @param queryParams - (Optional) Query params
     * @returns Observable<Transaction[]>
     */
    public incomingTransactions(publicAccount: PublicAccount,
                                queryParams?: QueryParams): Observable<Transaction[]> {
        return observableFrom(
            this.accountRoutesApi.incomingTransactions(publicAccount.publicKey, queryParams != null ? queryParams : {})).pipe(
            map((transactionsDTO) => {
                return transactionsDTO.map((transactionDTO) => {
                    return CreateTransactionFromDTO(transactionDTO);
                });
            }));
    }                
```       

### **How to use**
```

const accountAddress = Address.createFromRawAddress('SDRDGFTDLLCB67D4HPGIMIHPNSRYRJRT7DOBGWZY');
const accountPublicKey = '1026D70E1954775749C6811084D6450A3184D977383F0E4282CD47118AF37755';
const publicAccount = PublicAccount.createFromPublicKey('846B4439154579A5903B1459C9CF69CB8153F6D0110A7A0ED61DE29AE4810BF2',
        NetworkType.MIJIN_TEST);
const multisigPublicAccount = PublicAccount.createFromPublicKey('B694186EE4AB0558CA4AFCFDD43B42114AE71094F5A1FC4A913FE9971CACD21D',
        NetworkType.MIJIN_TEST);

 const accountHttp = new AccountHttp(APIUrl);
    
    
 describe('incomingTransactions', () => {
        it('should call incomingTransactions successfully', (done) => {
            accountHttp.incomingTransactions(publicAccount).subscribe((transactions) => {
                expect(transactions.length).to.be.greaterThan(0);
                done();
            });
        });
    });

```



        
## 5.交易的发送者

- Description:Fetch outgoing transactions from account     如果帐户是交易的发送人，则称交易相对于帐户是外发的。   
- Class:TransactionRepository.ts  
- Code sample:

```javascript
 /**
     * Gets an array of transactions for which an account is the sender a transaction.
     * A transaction is said to be outgoing with respect to an account if the account is the sender of a transaction.
     * @param publicAccount - User public account
     * @param queryParams - (Optional) Query params
     * @returns Observable<Transaction[]>
     */
    public outgoingTransactions(publicAccount: PublicAccount,
                                queryParams?: QueryParams): Observable<Transaction[]> {
        return observableFrom(
            this.accountRoutesApi.outgoingTransactions(publicAccount.publicKey, queryParams != null ? queryParams : {})).pipe(
            map((transactionsDTO) => {
                return transactionsDTO.map((transactionDTO) => {
                    return CreateTransactionFromDTO(transactionDTO);
                });
            }));
    }
```
### **How to use** 
```

const accountAddress = Address.createFromRawAddress('SDRDGFTDLLCB67D4HPGIMIHPNSRYRJRT7DOBGWZY');
const accountPublicKey = '1026D70E1954775749C6811084D6450A3184D977383F0E4282CD47118AF37755';
const publicAccount = PublicAccount.createFromPublicKey('846B4439154579A5903B1459C9CF69CB8153F6D0110A7A0ED61DE29AE4810BF2',
        NetworkType.MIJIN_TEST);
const multisigPublicAccount = PublicAccount.createFromPublicKey('B694186EE4AB0558CA4AFCFDD43B42114AE71094F5A1FC4A913FE9971CACD21D',
        NetworkType.MIJIN_TEST);

    const accountHttp = new AccountHttp(APIUrl);
 accountHttp.outgoingTransactions(publicAccount).subscribe((transactions) => {
                expect(transactions.length).to.be.equal(10);
                done();
            });

```


## 6.被确认的交易

- Description:Fetch transactions from account          返回已被确认的交易  
- Class:TransactionRepository.ts 

- Code sample 

```javascript
    /**
     * Gets an array of confirmed transactions for which an account is signer or receiver.
     * @param publicAccount - User public account
     * @param queryParams - (Optional) Query params
     * @returns Observable<Transaction[]>
     */
    public transactions(publicAccount: PublicAccount,
                        queryParams?: QueryParams): Observable<Transaction[]> {
        return observableFrom(
            this.accountRoutesApi.transactions(publicAccount.publicKey, queryParams != null ? queryParams : {})).pipe(
            map((transactionsDTO) => {
                return transactionsDTO.map((transactionDTO) => {
                    return CreateTransactionFromDTO(transactionDTO);
                });
            }));
    }
```

### **How to use** 
```

const accountAddress = Address.createFromRawAddress('SDRDGFTDLLCB67D4HPGIMIHPNSRYRJRT7DOBGWZY');
const accountPublicKey = '1026D70E1954775749C6811084D6450A3184D977383F0E4282CD47118AF37755';
const publicAccount = PublicAccount.createFromPublicKey('846B4439154579A5903B1459C9CF69CB8153F6D0110A7A0ED61DE29AE4810BF2',
        NetworkType.MIJIN_TEST);
const multisigPublicAccount = PublicAccount.createFromPublicKey('B694186EE4AB0558CA4AFCFDD43B42114AE71094F5A1FC4A913FE9971CACD21D',
        NetworkType.MIJIN_TEST);

const accountHttp = new AccountHttp(APIUrl);



it('should call transactions successfully', (done) => {
            accountHttp.transactions(publicAccount).subscribe((transactions) => {
                expect(transactions.length).to.be.greaterThan(0);
                done();
            });

```



## 7.未被确认的交易

- Description:Fetch unconfirmed transactions from account      返回未被确认的交易  
- Class:TransactionRepository.ts  
- Code sample 

```javascript
    /**
     * Gets the array of transactions for which an account is the sender or receiver and which have not yet been included in a block.
     * Unconfirmed transactions are those transactions that have not yet been included in a block.
     * Unconfirmed transactions are not guaranteed to be included in any block.
     * @param publicAccount - User public account
     * @param queryParams - (Optional) Query params
     * @returns Observable<Transaction[]>
     */
    public unconfirmedTransactions(publicAccount: PublicAccount,
                                   queryParams?: QueryParams): Observable<Transaction[]> {
        return observableFrom(
            this.accountRoutesApi.unconfirmedTransactions(publicAccount.publicKey, queryParams != null ? queryParams : {})).pipe(
            map((transactionsDTO) => {
                return transactionsDTO.map((transactionDTO) => {
                    return CreateTransactionFromDTO(transactionDTO);
                });
            }));
    }

```
### **How to use** 

```

const accountAddress = Address.createFromRawAddress('SDRDGFTDLLCB67D4HPGIMIHPNSRYRJRT7DOBGWZY');
const accountPublicKey = '1026D70E1954775749C6811084D6450A3184D977383F0E4282CD47118AF37755';
const publicAccount = PublicAccount.createFromPublicKey('846B4439154579A5903B1459C9CF69CB8153F6D0110A7A0ED61DE29AE4810BF2',
        NetworkType.MIJIN_TEST);
const multisigPublicAccount = PublicAccount.createFromPublicKey('B694186EE4AB0558CA4AFCFDD43B42114AE71094F5A1FC4A913FE9971CACD21D',
        NetworkType.MIJIN_TEST);

const accountHttp = new AccountHttp(APIUrl);



it('should call unconfirmedTransactions successfully', (done) => {
            accountHttp.unconfirmedTransactions(publicAccount).subscribe((transactions) => {
                expect(transactions.length).to.be.equal(0);
                done();
            });
        });
        
```







