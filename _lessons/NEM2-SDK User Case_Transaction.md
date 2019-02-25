---
layout: post
title:  "Usecase_Tracsaction"
permalink: /lessons/Usecase_Tracsaction/
---





# Transaction

- Language:TypeScript and JavaScript SDK 
- SDK-Version:v0.10.1-beta 
- MODEL:monitor

## Prerequisites
- Finish the getting started section
- Text editor or IDE
- NEM2-SDK or CLI
- Description:NIS supports two different kind of accounts: normal accounts and multsig (short for: multi signature) accounts.


## 1. Cosign 

- Description: Cosign an announce aggregate bonded transaction  
- Code sample:

```javascript
    /**
     * Send a cosignature signed transaction of an already announced transaction
     * @param cosignatureSignedTransaction - Cosignature signed transaction
     * @returns Observable<TransactionAnnounceResponse>
     */
    public announceAggregateBondedCosignature(
        cosignatureSignedTransaction: CosignatureSignedTransaction): Observable<TransactionAnnounceResponse> {
        return observableFrom(this.transactionRoutesApi.announceCosignatureTransaction(cosignatureSignedTransaction)).pipe(
            map((transactionAnnounceResponseDTO) => {
                return new TransactionAnnounceResponse(transactionAnnounceResponseDTO.message);
            }));
    }
```
### **How to use**


``` 
let account: Account;
    let account2: Account;
    let transactionHttp: TransactionHttp;
    let accountHttp: AccountHttp;
    let namespaceName: string;
    let mosaicId: MosaicId;
    let listener: Listener;
    const transactionHash = 'A192621335D733351A7035644F87338B0B9E36B3FAE61253E230B1A0D8BEA332';
    const transactionId = '5A2139FC9CD1E80001573357';
    
    
describe('announceAggregateBondedCosignature', () => {
        it('should return success when announceAggregateBondedCosignature', (done) => {
            const payload = new CosignatureSignedTransaction('', '', '');
            transactionHttp.announceAggregateBondedCosignature(payload)
                .subscribe((transactionAnnounceResponse) => {
                    expect(transactionAnnounceResponse.message)
                        .to.be.equal('packet 501 was pushed to the network via /transaction/cosignature');
                    done();
                });
        });
    });
```

## 2. info  


- Description : Fetch Transaction info   
-  Code sample 

```javascript
   /**
     * Gets a transaction for a transactionId
     * @param transactionId - Transaction id or hash.
     * @returns Observable<Transaction>
     */
    public getTransaction(transactionId: string): Observable<Transaction> {
        return observableFrom(this.transactionRoutesApi.getTransaction(transactionId)).pipe(map((transactionDTO) => {
            return CreateTransactionFromDTO(transactionDTO);
        }));
    }

    /**
     * Gets an array of transactions for different transaction ids
     * @param transactionIds - Array of transactions id and/or hash.
     * @returns Observable<Transaction[]>
     */
    public getTransactions(transactionIds: string[]): Observable<Transaction[]> {
        const transactionIdsBody = {
            transactionIds,
        };
        return observableFrom(
            this.transactionRoutesApi.getTransactions(transactionIdsBody)).pipe(map((transactionsDTO) => {
            return transactionsDTO.map((transactionDTO) => {
                return CreateTransactionFromDTO(transactionDTO);
            });
        }));
    }
```

### **How to use**


```javascript

let account: Account;
    let account2: Account;
    let transactionHttp: TransactionHttp;
    let accountHttp: AccountHttp;
    let namespaceName: string;
    let mosaicId: MosaicId;
    let listener: Listener;
    const transactionHash = 'A192621335D733351A7035644F87338B0B9E36B3FAE61253E230B1A0D8BEA332';
    const transactionId = '5A2139FC9CD1E80001573357';
    
    
transactionHttp.getTransaction(transactionHash)
                .subscribe((transaction) => {
                    expect(transaction.transactionInfo!.hash).to.be.equal(transactionHash);
                    expect(transaction.transactionInfo!.id).to.be.equal(transactionId);
                    done();
                });

transactionHttp.getTransaction(transactionId)
                .subscribe((transaction) => {
                    expect(transaction.transactionInfo!.hash).to.be.equal(transactionHash);
                    expect(transaction.transactionInfo!.id).to.be.equal(transactionId);
                    done();
                });
                
                
```


## 3.Mosaic    

- Description : Mosaic creation transaction 
- Code sample 

```javascript
    /**
     * Create a mosaic creation transaction object
     * @param deadline - The deadline to include the transaction.
     * @param mosaicName - The mosaic name ex: xem.
     * @param namespaceName - The namespace where mosaic will be included ex: nem.
     * @param mosaicProperties - The mosaic properties.
     * @param networkType - The network type.
     * @returns {MosaicDefinitionTransaction}
     */
    static create(deadline, mosaicName, namespaceName, mosaicProperties, networkType) {
        return new MosaicDefinitionTransaction(networkType, 2, deadline, new UInt64_1.UInt64([0, 0]), new NamespaceId_1.NamespaceId(namespaceName), new MosaicId_1.MosaicId(nem2_library_1.mosaicId(namespaceName, mosaicName)), mosaicName, mosaicProperties);
    }
```
### **How to use**
```
   it('should createComplete an AggregateTransaction object with MosaicDefinitionTransaction', () => {
        const mosaicDefinitionTransaction = MosaicDefinitionTransaction.create(
            Deadline.create(),
            'test-mosaic-name',
            'test-parent-name',
            MosaicProperties.create({
                supplyMutable: true,
                transferable: true,
                levyMutable: true,
                divisibility: 3,
                duration: UInt64.fromUint(1000),
            }),
            NetworkType.MIJIN_TEST,
        );
```


## 4.Namespace 
- Description: Register namespace transaction   
- Code sample 

```javascript
 /**
     * Create a root namespace object
     * @param deadline - The deadline to include the transaction.
     * @param namespaceName - The namespace name.
     * @param duration - The duration of the namespace.
     * @param networkType - The network type.
     * @returns {RegisterNamespaceTransaction}
     */
    public static createRootNamespace(deadline: Deadline,
                                      namespaceName: string,
                                      duration: UInt64,
                                      networkType: NetworkType): RegisterNamespaceTransaction {
        return new RegisterNamespaceTransaction(networkType,
            2,
            deadline,
            new UInt64([0, 0]),
            NamespaceType.RootNamespace,
            namespaceName,
            new NamespaceId(namespaceName),
            duration,
        );
    }               
```       

### **How to use**
```
import {deepEqual} from 'assert';
import {expect} from 'chai';
import {NamespaceName} from '../../../src/model/namespace/NamespaceName';
import {NamespaceId} from '../../../src/model/namespace/NamespaceId';

describe('NamespaceName', () => {

    it('should createComplete an NamespaceName object', () => {
        const namespaceNameDTO = {
            name: 'nem',
            namespaceId: new NamespaceId([929036875, 2226345261]),
            parentId: new NamespaceId([1431234233, 1324322333]),
        };

        const namespaceName = new NamespaceName(
            namespaceNameDTO.namespaceId,
            namespaceNameDTO.name,
            namespaceNameDTO.parentId,
        );

        deepEqual(namespaceName.namespaceId, namespaceNameDTO.namespaceId);
        deepEqual(namespaceName.parentId, namespaceNameDTO.parentId);
        expect(namespaceName.name).to.be.equal(namespaceNameDTO.name);
    });
});


 it('should createComplete an AggregateTransaction object with RegisterNamespaceTransaction', () => {
        const registerNamespaceTransaction = RegisterNamespaceTransaction_1.RegisterNamespaceTransaction.createRootNamespace(Deadline_1.Deadline.create(), 'root-test-namespace', UInt64_1.UInt64.fromUint(1000), NetworkType_1.NetworkType.MIJIN_TEST);
        const aggregateTransaction = AggregateTransaction_1.AggregateTransaction.createComplete(Deadline_1.Deadline.create(), [registerNamespaceTransaction.toAggregate(account.publicAccount)], NetworkType_1.NetworkType.MIJIN_TEST, []);
        const signedTransaction = aggregateTransaction.signWith(account);
        chai_1.expect(signedTransaction.payload.substring(0, 8)).to.be.equal('C9000000');
        chai_1.expect(signedTransaction.payload.substring(240, 256)).to.be.equal('4D0000004D000000');
        chai_1.expect(signedTransaction.payload.substring(320, signedTransaction.payload.length)).to.be.equal('02904E4100E803000000000000CFCBE72D994BE61B13726F6F742D746573742D6E616D657370616365');
    });
    
    
```




## 5.transfer  

- Description: Send transfer transaction  

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
