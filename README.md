# A Guide To The Corda Samples

---
## [yo-cordapp](https://github.com/corda/samples/tree/release-V4/yo-cordapp)
### Description
This CorDapp showcases the basic features of a state, contract, and flow.

In this CorDapp you can send Yo's! to all your friends running Corda nodes!

### Features Demonstrated
- Create simple states
- Create simple contracts
- Create simple flows
- Create simple validation of flows (e.g prevent transactions being sent to certain parties)
- Learn how to interact with the shell
### Use Cases
- Any CorDapp requires these basic components to communicate between nodes.
    - If you want to record information on-ledger (sharing it with other nodes), you'll need to create these basic components.
### Extending this sample
- Extend the state to hold more information than just a string.
- Create more complex flows (maybe recieving a 'yo!' triggers an automatic response of a `HelloState`)
- Try querying information from the vault in a flow.

## [whistleblower](https://github.com/corda/samples/tree/release-V4/whistleblower)
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

## [timesheet-example](https://github.com/corda/samples/tree/release-V4/timesheet-example)
### Description
This CordApp demonstrates the use of schemas in a transaction.

In this CorDapp, two nodes can exchange an IOU that encapsulates a `QueryableState` which returns an instance of a schema object. This schema object is the invoice and holds all of the information related to the IOU.
### Features Demonstrated
- Write a `MappedSchema`
- Write a `QueryableState`
- Use a `MappedSchema` in a flow
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

## [spring-webserver](https://github.com/corda/samples/tree/release-V4/spring-webserver)
### Description
This project defines a simple Spring webserver that connects to a Corda node via RPC.
### Features Demonstrated
- Create a Spring Boot server
- Create a RESTful API for Corda
### Use Cases
- Present information from the Corda nodes over HTTP
    - Maybe you are a museum that is leasing out art works. These lease agreements are kept track of using a Corda APP, but the museums' curators aren't familiar with Corda
		and don't want to have to worry about using the shell to retrieve this information. You could create a web interface that retrieves data from the vault and presents it in an
		HTML file.
- Trigger flows from a node over HTTP
	- Perhaps you're running a public node that allows users of your website to cast a ballot in some vote. You can do all of the login functionality
		from a standard web front end and create a REST API that triggers Corda functionality. You can guard end points the same way you would with a normal API.
- Display flow progress 
	- Perhaps you are managing an extensive registration process that requires a lot of back and forth between two parties. For example, student finance.
		It may be useful for the counterParty (in this instance, a student) to see where the application presently is (and what are the next steps). This could be done as a series of 
		subflows (in  which case, there could be a state keeping track of the students application), or a database update could occur during the flow and this is queried via an HTTP call
		to display progress in situ.
### Extending this sample
- This sample hasn't created any flows that the API end points are connected to. Using an application you've already developed, connect the API to your flows.

---

## [reference-states](https://github.com/corda/samples/tree/release-V4/reference-states)
### Description
This CorDapp demonstrates the use of reference states in a transaction and in the verification method of a contract.

This CorDapp allows two nodes to enter into an IOU agreement, but enforces that both parties belong to a list of sanctioned entities. 
This list of sanctioned entities is taken from a referenced `SanctionedEntities` state.


### Features Demonstrated
- Append a reference state to a transaction
### Use Cases
- When you want need the information from an updating state but you don't want to consume it in a transaction 
    - You are a company that maintains a number of projects and you work with a number of different partners on these projects. You have one state that maintains the list of active partners currently working with you. 
		This state periodically is updated when new partners are added or old ones are removed.	When a new project is commenced, a new state is created for that project. That project assigns partners to work on it and this list is maintained in its state. 
		Perodically, this project state is updated to track the progress of that project and to add or remove partners who are actively working on it. Whenever this state is updated, you want to be sure that all the partners that you have working on that
		project are actively partnered with you. To make sure this is the case, you want to include a reference to your active partners state. NOTE: It may be the case that the `activePartners` state used by the initiating party is outdated. In order
		to circumvent this issue, you'd want to include a referencing state that holds a [StatePointer](https://github.com/corda/token-sdk/blob/master/design/design.md#StatePointer) (of type `LinearID`) that points to the activePartners state. 

### Extending this sample
- Add a command for transferring an IOU to a new beneficiary and require that this beneficiary is a sanctioned entity.
- Add a StatePointer to the referenced state and use that to point to the SanctionedEntity to ensure that the SanctionedEntity list is the most up to date. 

---

## [pigtail](https://github.com/corda/samples/tree/release-V4/pigtail)
### Description
This CorDapp sample demonstrates how to setup a Braid service and JS client to interact with a Corda backend.
### Features Demonstrated
- Bootstrap a braid server through a CordaService
- Add flows and services to braid server
- Setup config for the braid server including port number and HTTPS vs HTTP
- Expose HTTP endpoints using a Braid Service that can be consumed by a Javascript client
### Use Cases
- [SEE SPRINGBOOT]()
### Extending this sample
- Take an application you've already built (or one of the other samples) and expose it's flows using the Braid service.

---

## [oracle-example](https://github.com/corda/samples/tree/release-V4/oracle-example)
### Description
This CorDapp is a simple example of how to structure an oracle service that provides querying and signing abilities

This CorDapp implements an oracle service that allows nodes to:

- Request the Nth prime number
- Request the oracle's signature to prove that the number included in their transaction is actually the Nth prime 
  number
### Features Demonstrated 
- How to write an oracle
- How to write flows for an oracle 
- How to request information from an oracle
- How to get an oracle to sign some information
- How to add an oracle as a required signer for a transaction
### Use Cases
- Request information from a trusted third party
    - All corDapps HAVE to be deterministic so that they can be verified. As a result, you can't use external information straight in your
		contracts. However, there may be occasions when you require a piece of third party information. One way of obtaining that information is
		to request it from an oracle class. For instance, suppose you have an agreement established in a state on the ledger that states, if it rains
		on wednesday, partyB will owe partyA £100. You could retrieve the weather report on any given day by requesting it from an oralce (including a [time-window](https://docs.corda.net/key-concepts-time-windows.html?highlight=time%20window) in your transaction
		could guarantee that the informaton was obtained on a wednesday)
- Have information signed for by a trusted third party
    - There is some infromation already available to you, but in order for your counterparty to accept it, they want some assurance that it is legitimate.
		For example, in order to transact with you the counterparty wants to guarantee that your company has registered with a certain organisation.
		Now, we could provide a certification that claims this to be true but perhaps the counterparty wants that information to be signed off on by a trusted third 
		party before it will accept the transaction. We could send this certification to the trusted third party (the oracle) and have them sign off on the information.
### Extending this sample
- Create an oracle for information that can't be deterministically generated by the nodes (E.G access a weather report API or pull ina  file stored by the oracle)

---
## [observable-states](https://github.com/corda/samples/tree/release-V4/observable-states)
### Description
This CorDapp shows how Corda's observable states feature works. Observable states is the ability for nodes who are not 
participants in a transaction to still store them if the transactions are sent to them.

In this CorDapp, we assume that when a seller creates some kind of `HighlyRegulatedState`, they must notify the state 
and national regulators. There are two ways to use observable states:
### Features Demonstrated 
- Manually send a non-signing party a copy of a signed transaction
### Use Cases
- Have a party store a state in their vault without having to be a required signer on it
    - In a transaction you have a third party that you don't want to give the opportunity to block your transactions but has to be made aware
		of a transaction that has taken place. Perhaps you are maintaining a closed auction and, upon the auction closing, you are required by a regulator
		to announce the winner of the bid to all involved parties and the regulator. You don't want the losing parties to be able to block the closing of the 
		auction (for self interest, they may decide to not sign), but they do need to see the finished state. 
### Extending this sample
- Automate the sending of a signed transaction to a non-signing party via the `FinalityFlow`

---
### [obligation-cordapp](https://github.com/corda/samples/tree/release-V4/obligation-cordapp)
### Description
This CorDapp comprises a demo of an IOU-like agreement that can be issued, transfered and settled confidentially. 

The CorDapp allows you to issue, transfer (from old lender to new lender) and settle (with cash) obligations. It also 
comes with an API and website that allows you to do all of the aforementioned things.
### Features Demonstrated 
- Use anonymous identities in transactions
- Create an obligation
- Transfer ownership of an obligation between parties
- Connect AngularJS to a Corda backend in java and kotlin
- Compare token types
### Use Cases
- Create financial instruments that can change ownership
    - You may have lent money to another party and created an obligation on Corda to record this transaction. You may want to be able to sell this obligation
		to another party in the future (so that, now, the borrower owes the new lender the money) 
- Create obligations with anonymised participants
    - If you're creating a state that may frequently change hands (e.g an obligation that's intended to be used as a financial instrument) you may not want the history
		of the lender parties to be kept on ledger. By anonymising the parties involved during the transaction, you can keep this information hidden.
### Extending this sample
- In the `ObligationBaseFlow`, in the internal class `SignxFlowNoChecking`, add verification for the signed transaction.
- Add a time-window for the period of time that an obligation can be traded
- Add a sanction list for the entites that can be transferred the obligation(see [reference-states]())


---
### [negotiation-cordapp](https://github.com/corda/samples/tree/release-V4/negotiation-cordapp)

### Description
This CorDapp shows how multi-party negotiation is handled on the Corda ledger, in the absence of an API for user 
interaction.

A flow is provided that allows a node to propose a trade to a counterparty. The counterparty has two options:

* Accepting the proposal, converting the `ProposalState` into a `TradeState` with identical attributes
* Modifying the proposal, consuming the existing `ProposalState` and replacing it with a new `ProposalState` for a new 
  amount

Only the recipient of the proposal has the ability to accept it or modify it. If the sender of the proposal tries to 
accept or modify the proposal, this attempt will be rejected automatically at the flow level.

### Feature Demonstrated
- Accessing `FlowSession.counterParty` to control who initiates a flow.
- Separating a business use case into multiple flow.
- Throwing FlowExceptions to manually abort flows. 
### Use Cases
- Performing business logic on a received state before finalising
    - It's not always going to be the case that a state received from a flow can be immediately approved.
  For example, with a real estate registration, there may be a verification process that cant be automated and has to occur off ledger first (e.g a certificate of ownership in an attachment may need to be checked by a human entity). You can use this demostrated technique to divide this flow up where necessary to allow for the off-ledger components.
- Negotiating terms of a contract
    - It may be the case that two interacting bodies have two different expectations of the information given in a state. The process of modifying the state and proposing alternatives may be automated (e.g the alternate flows are automtically triggered).

### Extending The Sample
- Create a negotiation bot (a buyer and a seller) that are negotiating an art sale. The buyer has an upper limit on the money they will accept for the trade and the seller has a lower limit on the money they will accept. They are trying to minimise/maximise the agreed upon price respectively. Create a modifyable state that alters these proposals until an agreement is reached. 

---

### [implicit-cordapp-upgrades](https://github.com/corda/samples/tree/release-V4/implicit-cordapp-upgrades)

### Description
This CorDapp shows how to carry out implicit upgrades of a CorDapp, by writing flows and contracts in a backwards compatible manner and taking advantage of Signature Constraints. 

Implicit upgrades to the contract involve adding the new versions of a CorDapp to the whitelist (list of accepted CordApps on a network) and then using the version numbers to delegate to the correct flow handler for that version of an app. This allows a node to 'lazy upgrade' (upgrade only when they want to interact with a node that requires them to) and to continue interacting with other nodes running older versions of that app.
### Feature Demonstrated 
- Versioning flows via annotations
- Use versioning to delegate flow handlers
- Issue upgraded contracts to your test nodes via a .sh script
### Use Cases
- When you don't need an atomic upgrade, but you want to change features of a contract
    - You've issued an IOU that has an inefficient verification process that's slowing down the speed of transactions. Nodes are free to use the older version because the integrity of the agreement isn't compromised, but some nodes may choose to use the new version to speed up the operation of the dAPP.
### Extending The Sample
- Take an old application that you've built  (or take the cordapp-example code) and create a second version of the contract with a new verification method. Create a flow delegation to handle the multiple versions.