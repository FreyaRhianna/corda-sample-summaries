# A Guide To The Corda Samples

---

## [Whistleblower](https://github.com/corda/samples/tree/release-V4/whistleblower)
### Description
This CorDapp is a simple showcase of confidential identities (i.e. anonymous public keys).

A node (the *whistle-blower*) can whistle-blow on a company to another node (the *investigator*). Both the 
whistle-blower and the investigator generate anonymous public keys for this transaction, meaning that any third-parties 
who manage to get ahold of the state cannot identity the whistle-blower or investigator.
### Features Demonstrated
- Generate anonymous identities (using `SwapIdentitiesFlow`)  

- Sign a transaction using anonymous keys
### Use Cases
- Have a three way communication where the two noninitiating participants don't know eachothers identities
    - A central orangiser wants to exchange the same information to two potential investors, but the two potential investors
		shouldn't know eachothers legal identities.
	- Two parties are in a competition organised by a third party. The third party wishes to give the same information to the two parties
		but doesn't want them to know eachothers legal identities.
	- Several parties have placed anonymous bids. The third party needs to send information to the parties who have successfully placed the bid
    	but doesn't want to leak who has/hasn't placed a bid. 
- Use a state for reference but keep the parties involved in that state anonymous
	- Hide the purchase trail of private buyers but allow for the validation of the issuer  
- In persisted data, obfuscate the originator of a transaction 
	- purchasing multiple train tickets: keep the journeys from being related if the database leaks

### Extending this sample

- In the `BlowWhistleFlowResponder` class, the `checkTransaction` function is incomplete. Add some validation for the transaction.
- Once a complaint has been lodged, a party may want to take action. Create a state for these actions and a flow that references the original complaint. 

---

## [TimeSheet](https://github.com/corda/samples/tree/release-V4/timesheet-example)
### Description
This CordApp demonstrates the use of schemas in a transaction.

In this CorDapp, two nodes can exchange an IOU that encapsulates a `QueryableState` that returns an instance of a schema object. This schema object is the invoice and holds all of the information related to the IOU.
### Features Demonstrated
- Write a MappedSchema
- Write a QueryableState
- Use a MappedSchema in a flow
- Create an Oracle to retrieve external information
- Use data classes to transfer information between Oracles and nodes
### Use Cases
- Export the information in a state into a database compatable schema. 
    - You may have previously existing databases that you want to store state information in, off-ledger. You can write a schema that 
		corresponds to the required fields and use this to hold your state information.
- You might want to create a custom table with your own defined attributes in your node's database. You can the query the vault according to these attributes.
	- One of your flows may use states that hold evolving information about the amount of money left in an IOU agreement. Maybe you want to send out
		a notice to all nodes that owe you more than a threshold (maybe those who owe you more than £100). You could use this newly defined table
		to query the vault specifically on the 'amountOwed' attribute. 
- Create relational joins between the node's tables and your organisation's tables
	- You may use Corda to keep track of purchases. Perhaps you have to insist that the productIDs of those items sold corresponds
		to some productId you use internally to keep track of your stock. 
### Extending this sample

- There is no code in `SalaryRateOracle` that handles the situation where a pay rate is missing.
- In `InvoiceContract` write a clause that supports a partial payment.
- Create code that registers a new job when it commences and links the invoice to that job.

---

## [Spring-webserver](https://github.com/corda/samples/tree/release-V4/spring-webserver)
### Description

### Features Demonstrated

### Use Cases

### Extending this sample