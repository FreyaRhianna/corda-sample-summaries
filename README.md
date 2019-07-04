# A guide to the Corda samples

---

## [blacklist](https://github.com/corda/samples/tree/release-V4/blacklist) (Kotlin)


### Description

This CorDapp allows nodes to reach agreement over arbitrary strings of text, but only with parties that are not 
included in the blacklist uploaded to the nodes.

The blacklist takes the form of a jar including a single file, `blacklist.txt`. `blacklist.txt` lists the following 
parties as being banned from entering into agreements:

* Crossland Savings
* TCF National Bank Wisconsin
* George State Bank
* The James Polk Stone Community Bank
* Tifton Banking Company

The blacklist jar is uploaded as an attachment when building a transaction, and used in the `AgreementContract` to 
check that the parties to the `AgreementState` are not blacklisted.

### Features Demonstrated
- Learn how to handle and parse transaction attachments
- Working with X500 identities
- Hash verification

### Cases

Although this example CorDapp restricts agreement over **arbitrary strings of text** (contained in `AgreementState`) to parties not included
in the blacklist of **organisations**, this same blacklist functionality can be used to potentially limit:

a) agreements consisting of many other types of states.
- the blacklist is enforced in the `AgreementContract` where an overlap of the participant's organisation
against the blacklist will throw a 'IllegalArgumentException - Failed Requirement'. If you have a
custom state, you can use the same code in the overloaded verify function of your associated contract. 

b) participants based on features OTHER than their organisation.
- The `AgreementContract` uses the CordaX500Name to identify the participant organisations and compare it to the
attached blacklist. It's important to note that additional properties are available through the same
participant identity including: 
    - commonName, 
    - organisationUnit, 
    - organisation, 
    - locality, 
    - state, 
    - and country.

**Whitelisting**
Another useful application of this sample is to flip the requirement condition to turn the attachment
into a whitelist. In `AgreementContract`, using a statement such as
 `blacklistedCompanies.toSet().containsAll(participantsOrgs)` with the infix function `using` would throw an exception if any participant
 is NOT on the attachment of approved organisations.

### Extending the Sample

With very
little modification to the sample code you can create blacklists on these other predicates.

- Issuing tokens (see new tokenSDK) to holders based on their region (state or country). *I.E.* A special token that
represents value only relevant to a specific area. Possibly a state tax rebate where some states do
not have the program so you want to exclude them. Or a real-estate
property where ownership excludes certain entities.
- Restricting agreements with participants based on some off-ledger criteria in your organization. For example, a Multi-Level-Marketing
firm that does drop-shipping agreements with member-distributors. In the case that it is the requirement for
a member-distributor to maintain a monthly purchase amount, members who fail to meet a quota can be placed on the 
blacklist.

### Notes
An important thing to consider with a blacklist in attachment form is that it has the advantage of allowing
you to maintain the list with **off-ledger** facts. There are other ways enforce a blacklist in Corda as well. For instance,
you could maintain a blacklist state and use this as an input to transactions, or you could also build (blacklist-like)
conditions into your contracts. 

A use case where a blacklist attachment might **NOT** be suitable is saying "a party can only hold X tokens". It's possible to
hold a blacklist, but it is much better off to query the vault for the current number of tokens issued to the party.


---

## [corda-nodeinfo](https://github.com/corda/samples/tree/release-V4/corda-nodeinfo) (Kotlin)


### Description

Allows one to get some rudimentary information about a running Corda node via RPC

Useful for debugging network issues, ensuring flows have loaded etc.

After cloning, use the _getInfo_ gradle task to retrieve node information.

```
./gradlew getInfo -Phost="localhost:10006" -Pusername="user1" -Ppassword="test"

> Task :getInfo
Logging into localhost:10006 as user1
Node connected: O=PartyA, L=London, C=GB
Time: 2018-02-27T11:30:37.729Z.
Flows: [com.example.flow.ExampleFlow$Initiator, net.corda.core.flows.ContractUpgradeFlow$Authorise, net.corda.core.flows.ContractUpgradeFlow$Deauthorise, net.corda.core.flows.ContractUpgradeFlow$Initiate, net.corda.finance.flows.CashConfigDataFlow, net.corda.finance.flows.CashExitFlow, net.corda.finance.flows.CashIssueAndPaymentFlow, net.corda.finance.flows.CashIssueFlow, net.corda.finance.flows.CashPaymentFlow]
Platform version: 2
Current Network Map Status -->
-- O=PartyA, L=London, C=GB @ localhost
-- O=Controller, L=London, C=GB @ localhost
-- O=PartyB, L=New York, C=US @ localhost
-- O=PartyC, L=Paris, C=FR @ localhost
-- O=Notary, L=London, C=GB @ localhost
Registered Notaries -->
-- O=Notary, L=London, C=GB
-- O=Controller, L=London, C=GB
```

### Features Demonstrated

- connecting to nodes using `CordaRPCClient`
- creating queries with `CordaRPCOps` [link](https://docs.corda.net/api/javadoc/net/corda/core/messaging/CordaRPCOps.html)
- connecting directly to queues with AMQP

### Cases

- This sample is useful when you want to retrieve a topology of the network map
    - an app that is triggered by changes in node participation
    - tracking node behaviors over time
- Flow availability
    - putting controls on available flows on the network
    - monitoring

### Extending the Sample

- Reference the [CordaRPCOps](https://docs.corda.net/api/javadoc/net/corda/core/messaging/CordaRPCOps.html) docs and issue additional commands or queries to the connected node
- Export info to local logs for analysis or tracking
- Build a front-end to interact with your app through CordaRPCOps

### Notes
See Docs related to the [CordaRPCClient](https://docs.corda.net/tutorial-clientrpc-api.html)

---


## [cordapp-example](https://github.com/corda/samples/tree/release-V4/cordapp-example) (Kotlin, Java)


### Description
This CorDapp allows nodes to agree IOUs with each other, as long as they obey the following contract rules:

- The IOU’s value is strictly positive
- A node is not trying to issue an IOU to itself

There is more extensive documentation in a tutorial format available [here](This CorDapp is documented [here](http://docs.corda.net/tutorial-cordapp.html))

### Features Demonstrated
- Contract, State, and Flow structures
- Providence and evolving facts through the use of LinearState interface
- mapping Schemas and use of QueryableState
- using [ProgressTracker](https://docs.corda.net/api/kotlin/corda/net.corda.core.utilities/-progress-tracker/index.html) and steps

### Cases

Any case where you need to agree that a certain party/parties are indebted or required to execute performance
of some criteria to satisfy the counter-party. 

### Extending the Sample

The paradigm of an IOU contract can apply to many real world uses which extend
on the same code-base and structure of this example.

**Escrows** - You can take the IOU agreement and modify the contract to verify a certain condition
creating a conditional escrow (either on-ledger or off, see [Oracles](https://docs.corda.net/key-concepts-oracles.html))

**Tokens/States** - The IOU example uses a simple Integer value to represent the amount owed. This
is the most basic realization of the IOU and misses possibly important data such as currency, non-monetary assets, fractional stakes.
You can create an IOU using Corda [Token SDK](https://github.com/corda/token-sdk) to generate IOUs on many types of defined assets including;
DigitalCurrency, Fiat, Fungible Tokens, Non-Fungible Tokens, Evolving Tokens, and more!

**Time-Windows** - Extend your IOU to include a [Time Window](https://docs.corda.net/key-concepts-time-windows.html) in order to add characteristics such as:
interest accrual, deadlines, depreciation/appreciation etc.

**Split and Merge** - IOU can be extended to handle further complexity than just a single lender / borrower. With
the basic IOU state, you can consume an existing IOU and split the value across multiple output IOUs. Likewise,
you can take multiple IOUs as input states and then combine them into a single IOU.

### Notes

None.

---


## [explicit-cordapp-upgrades](https://github.com/corda/samples/tree/release-V4/explicit-cordapp-upgrades) (Kotlin)


### Description

This CorDapp shows the end-to-end process of upgrading contracts in Corda, using the explicit Contract Upgrade Flow. A demonstration of implicit upgrades using Signature Constraints can be found in the Implicit Cordapp Upgrades app (https://github.com/corda/samples/tree/release-V4/implicit-cordapp-upgrades)

The upgrade takes place in four stages:

1) Create a replacement contract implementing the UpgradedContract interface and the accompanying state
2) Bundle the replacement contract and state into a CorDapp and install it on each node
3) For each state you wish to upgrade the contract of, authorise the contract upgrade for that state on each node
4) On a single node, run the contract upgrade for each state you wish to upgrade the contract of

### Features Demonstrated

- Establishing a connection to nodes via CordaRPCClient
- Running flows on states filtered by associated contract ID
- Using Corda's `ContractUpgradeFlow` to Authrise and then Upgrade state instances
- Implementing `UpgradedContract` interface.

### Cases

General reason you would want to execute an Explicit Contract Upgrade could be:

- Changing the verification of transactions based on some new requirement agreed by all 
parties. (Note: if new verify logic is not required by all participants you can bypass potential
contract upgrades by using [Clauses](https://docs.corda.net/releases/artifactory-test/tutorial-contract-clauses.html#what-are-clauses-and-why-use-them))

See the following links for more in-depth description of scenarios suited to Explicit Contract Upgrades

[Contract Upgrades and Constraints in V3](https://medium.com/corda/contract-upgrades-constraints-in-v3-d5b14d5fb258)

[Upgrading Contracts](https://docs.corda.net/head/contract-upgrade.html)


### Extending the Sample

- The implementation of the UpgradedContract interface function `upgrade` contained in `com.upgrade.new.NewContract` 
defines the upgrade path in regards to transitioning to new states linked to the new contract. In this example
we can see that the states are not modified but you may wish to transition to new state definitions during the contract
upgrade. You can modify this upgrade logic.

- You may also find this code useful in an automation of updating off-ledger agreements which have a corresponding ledger representation. For example,
`UpgradeContractClient` in Client.kt makes calls to Corda's `ContractUpgradeFlow` with the `NewContract` class as a parameter. By pushing changes in contract properties into a template engine, or by creating a transaction with the new contract properties stored in an attachment; you can
dynamically generate the `NewContract` and execute an automated explicit update on new contract requirements.

### Notes

Also see [Implicit Cordapp Upgrades](https://github.com/corda/samples/tree/release-V4/implicit-cordapp-upgrades)

---


## [flow-db](https://github.com/corda/samples/tree/release-V4/flow-db) (Kotlin)


### Description

This CorDapp provides a simple example of how the node database can be accessed in flows. In this case, the flows maintain a table of cryptocurrency values in the node's database. There are three flows:

The CorDapp defines three flows:

AddTokenValueFlow, which adds a new token to the database table with an initial value
UpdateTokenValueFlow, which updates the value of an existing token in the database table
QueryTokenValueFlow, which reads the value of an existing token from the database table
Under the hood, the database accesses are managed by the CryptoValuesDatabaseService CordaService.

Be aware that support of database accesses in flows is currently limited:

The operation must be executed in a BLOCKING way. Flows don't currently support suspending to await an operation's response
The operation must be idempotent. If the flow fails and has to restart from a checkpoint, the operation will also be replayed

### Features Demonstrated

- Testing with MockNetworks and Unit-tests
- Initiating a jdbcSession through a node's [ServiceHub](https://docs.corda.net/api/kotlin/corda/net.corda.core.node/-service-hub/index.html)
- Creating a custom cordaService to handle serialization/deserialization of tokens
- Defining custom SQL queries and mapping parameters to the queries

### Cases

- Accessing Legacy databases / integration with existing systems
- Storing reference data uncoupled from a state
    - table of crypto-token values (this sample)
    - addresses/information of entities who do not have an on-ledger representation
    - URLs or mirror links
- GDPR compliance (some customer data may not be suitable on an immutable ledger)

### Extending the Sample

- Interact with a different database (see [Node Configuration](https://docs.corda.net/head/node-database.html))
- Implement more complex SQL queries in `CryptoValuesDatabaseService`
- Execute custom [sub-flows](https://docs.corda.net/flow-state-machines.html#sub-flows) based on SQL return values

### Notes

By default nodes are configured to use an H2 database but PostgreSQL and 
SQLserver can easily by used instead.

---


## [flow-http](https://github.com/corda/samples/tree/release-V4/flow-http) (Kotlin)


### Description
This CorDapp provides a simple example of how HTTP requests can be made in flows. In this case, the flow makes an HTTP request to retrieve the original BitCoin readme from GitHub.

Be aware that support of HTTP requests in flows is currently limited:

The request must be executed in a BLOCKING way. Flows don't currently support suspending to await an HTTP call's response
The request must be idempotent. If the flow fails and has to restart from a checkpoint, the request will also be replayed

### Features Demonstrated
- Http request from within a flow
- Using libraries within your flows

### Cases
- You want to interact through flows with API endpoints
    - Have transaction details forwarded to an analytics service etc.
    - Do a GET request to retrieve details
- Retrieve (non-critical) data from the web to modify states or execute conditional behavior
    - Parse a web-page

### Extending the Sample

- Try interacting with a 3rd party API service
- Build in error handling for failed responses
- Take transaction details and issue http receipts with details.

### Notes

Important! This simple request must be executed in a BLOCKING manner. If you are wishing
to use HTTP request responses in state verification, or take advantage of Corda's state machine serialization behavior please look
at implementing [Oracles](https://docs.corda.net/key-concepts-oracles.html) - to ensure multiple nodes are
presented with the same response.

---

## [heartbeat](https://github.com/corda/samples/tree/release-V4/heartbeat) (Kotlin)


### Description

This CorDapp is a simple showcase of scheduled activities (i.e. activities started by a node at a specific time without direct input from the node owner).

A node starts its heartbeat by calling the StartHeartbeatFlow. This creates a HeartState on the ledger. This HeartState has a scheduled activity to start the HeatbeatFlow one second later.

When the HeartbeatFlow runs one second later, it consumes the existing HeartState and creates a new HeartState. The new HeartState also has a scheduled activity to start the HeatbeatFlow in one second.

In this way, calling the StartHeartbeatFlow creates an endless chain of HeartbeatFlows one second apart.

### Features Demonstrated
- initializing Java `Instant` object to represent time from Epoch
- implementation of `ScheduledState` interface on the state object so defined flow will execute at the passed Instant.
- user of `ProgressTracker` to track stages of Flow and subFlow

### Cases
- Issue monthly statements on a schedule
    - generate a balance report
    - create an Obligation or IOU for a recurring time-frame or point in the future
- Have your CorDapp delay a Flow in anticipation of a future event
    - wait for an on-ledger independent transaction
    - wait for an off-ledger condition through the use of an [Oracle](https://docs.corda.net/key-concepts-oracles.html)
- Creating a continuous "clock-cycle" like execution (as this sample does)
    - execute batch transactions through regular cycles

### Extending the Sample
- Override the `Instant` parameter of the HeartState primary constructor to set different delays
- Add conditions to create dynamic schedules based on some state of the world
    - For example, query the network map for number of Notaries and vary delay a flow based on network resources.
- Use extended recurring subFlows (similar to the sample) with varying delays to compose a song or return an artistic output. 

---
## [yo-cordapp](https://github.com/corda/samples/tree/release-V4/yo-cordapp) (Kotlin)
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
- Try [querying information from the vault](https://docs.corda.net/api-vault-query.html) in a flow.

## [whistleblower](https://github.com/corda/samples/tree/release-V4/whistleblower) (Kotlin)
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

## [timesheet-example](https://github.com/corda/samples/tree/release-V4/timesheet-example) (Kotlin)
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

- Add code in `SalaryRateOracle` to handle the case where a pay rate is missing.
- In `InvoiceContract` write a clause that supports a partial payment.
- Create code that registers a new job when it commences and links the invoice to that job.

### Notes
See the API for [QueryableState](https://docs.corda.net/api-persistence.html?highlight=queryable)

---

## [spring-webserver](https://github.com/corda/samples/tree/release-V4/spring-webserver) (Kotlin)
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

## [reference-states](https://github.com/corda/samples/tree/release-V4/reference-states) (Kotlin)
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

### Notes
When you use a reference state in a transaction, the contract for that state is also not executed. 

---

## [pigtail](https://github.com/corda/samples/tree/release-V4/pigtail) (Kotlin)
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

## [oracle-example](https://github.com/corda/samples/tree/release-V4/oracle-example) (Kotlin)
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
## [observable-states](https://github.com/corda/samples/tree/release-V4/observable-states) (Kotlin)
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
### [obligation-cordapp](https://github.com/corda/samples/tree/release-V4/obligation-cordapp) (Kotlin, Java)
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
### [negotiation-cordapp](https://github.com/corda/samples/tree/release-V4/negotiation-cordapp) (Kotlin)

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

### [implicit-cordapp-upgrades](https://github.com/corda/samples/tree/release-V4/implicit-cordapp-upgrades) (Kotlin)

### Description
This CorDapp shows how to carry out implicit upgrades of a CorDapp, by writing flows and contracts in a backwards compatible manner and taking advantage of Signature Constraints. 

Implicit upgrades to the contract involve adding the new versions of a CorDapp to the whitelist (list of accepted CordApps on a network) and then using the version numbers to delegate to the correct flow handler for that version of an app. This allows a node to 'lazy upgrade' (upgrade only when they want to interact with a node that requires them to) and to continue interacting with other nodes running older versions of that app.
### Features Demonstrated 
- Versioning flows via annotations
- Use versioning to delegate flow handlers
- Issue upgraded contracts to your test nodes via a .sh script
### Use Cases
- When you don't need an atomic upgrade, but you want to change features of a contract
    - You've issued an IOU that has an inefficient verification process that's slowing down the speed of transactions. Nodes are free to use the older version because the integrity of the agreement isn't compromised, but some nodes may choose to use the new version to speed up the operation of the dAPP.

See the following links for more in-depth description of scenarios suited to Implicit Contract Upgrades

[Contract Upgrades and Constraints in V3](https://medium.com/corda/contract-upgrades-constraints-in-v3-d5b14d5fb258)

[Upgrading Contracts](https://docs.corda.net/head/contract-upgrade.html)


### Extending The Sample
- Take an old application that you've built  (or take the cordapp-example code) and create a second version of the contract with a new verification method. Create a flow delegation to handle the multiple versions.


### Notes
Also see [Explicit Cordapp Upgrades](https://github.com/corda/samples/tree/release-V4/explicit-cordapp-upgrades)

---

## [attachment-demo](https://github.com/corda/corda/tree/master/samples/attachment-demo) (Kotlin)
### Description
This demo brings up three nodes, and sends a transaction containing an attachment from one to the other.

### Features Demonstrated
- Establishing rpc connection through CordaRPCClient and interacting with nodes through 
CordaRPCOps
- Hashing a file to SecureHash.SHA256
- Uploading attachment and verifying storage on node through hash
- Downloading attachments from a node's corresponding Web endpoint.

### Use Cases
Attachments are useful for:
- Inclusion of legal prose or metadata
    - you have a corresponding invoice with a transaction
    - you have a legal off-ledger contract backing the on-ledger representation
    - you want to store additional data such as URLs, files, etc.; related to the transaction
- One method of implementing [oracles](https://docs.corda.net/oracles.html) (suitable for large, static, and low value data)

### Extending The Sample
- try adding multiple attachments
- use `AttachmentQueryCriteria` to filter attachments and run complex queries with boolean operators
- go beyond a "two party trade flow" to send attachments to multiple nodes

### Notes

See the official docs from more information about [attachments](https://docs.corda.net/tutorial-attachments.html?highlight=attachments)


##[trader-demo](https://github.com/corda/corda/tree/master/samples/trader-demo) (Kotlin)

### Description
This CorDapp demonstrates the manners by which you can configure a responder flow.

The CorDapp has five nodes; Bank A, Bank B, Bank of Corda, a NonLogging Bank, and a notary bank. Four of the five banks will print transaction information because that is what is defined in the `LoggingBuyerFlow` (which subclasses, and therefore takes priority over, `BuyerFlow`). 
The NonLogging Bank however will not print out this transaction information because it has been configured in the `deployNodes` task to explicitly user `BuyerFlow`.
### Features Demonstrated 
- Use of subclassing to override a flow responder
- Use of explicit configuration to override a flow responder.
### Use Cases
- Subclassing a flow
    - Extending a third party application
        - You may be using a CorDapp that's been created by another party that you wish to add features to. Instead of recreating the app from scratch, you could extend the features of the app by subclassing the flows that need to change and adding new functionality.
    - Modifying a generic flow to suit a specific business case
        - You can add features necessary for a specific business case to a pre-existing flow.
    - Borrowing features from a flow
        - If you are creating a new flow that is very similar to one already in use in your app, it might be worth considering simply extending the behaviour.
- Explicitly configuring a different responder
    - Modifying a third party application 
        - A third party may have provided a CorDapp but you need to completely override some of its elements to suit your business needs
    - Redefining a generic flow to suit a specific business case 
        - It may be the case that a parent flow shares very few, or no, features with the flow necessary for a particular business case. In this instance, you don't want to subclass the original flow but instead create an entirely new one.
- Manage many nodes running the sample application with different responses.
    - Overriding flows instead of creating separate CorDapps can be a simple way of handling the business case where nodes need to run variants on the parent flow.
### Extending The Sample
- Use different means (subclassing and explicit configuration) to override the `SellerFlow` and change the behaviour of the initiating flow for different nodes.
### Notes 
- To read more about ovveriding flows, click [here](https://docs.corda.net/head/flow-overriding.html).
- **WARNING**: Pay careful attention to the annotations of the subclassing flows. The responders require `@InitatedBy` but initiators that subclasss should **not** have the `@Initiating` annotation.
- **WARNING**: The more overriding flows you have, the more complex it will be to ensure that the sequence of steps in the initiators and the responders is compatible. If your CorDapp is growing complex, consider whether you should be using a new CorDapp with different functionality. 

---

## [simm-valuation-demo](https://github.com/corda/corda/tree/master/samples/simm-valuation-demo) (Kotlin, Java)
### Description
This CorDapp gives a demonstration of how third party libraries can be used by a Corda node.

The CorDapp uses [OpenGamma's](http://www.opengamma.com) proprietary model to showcase the SIMM process and integrate it within the app. 

**NOTE** Due to the proprietary nature of this service, the actual sample is taking stub data (faked data) and using that instead of the opengamma analytics engine. The format of the data is identical. 
### Features Demonstrated
- How to integrate a third party library with a Corda node
- Use of stub data for testing
### Use Cases
- If you need to use processes external to your Corda node
    - Not all use cases require simple processing. It may be necessary to encapsulate some processing into a library and then to integrate this library with your Corda node.
- Accessing third party libraries
    - Most business logic is spread across a number of providers. Using this as an example, you can integrate these services with your Corda node.
### Extending The Sample
- If you have a license from OpenGamma, you can swap out the stub data for the analytics engine (which we will be happy to demonstrate)
### Notes
**WARNING** The analytics engine has been replaced with stub data in this demo. Results obtained by this sample should not be used as a source of information.

---

## [notary-demo](https://github.com/corda/corda/tree/master/samples/notary-demo) (Kotlin)
### Description
This CorDapp demonstrates how to use a single-node notary vs a distributed notary, and also gives examples of the potential pluggable consensus algorithms you can use for that distributed notary.

The CorDapp shows a party getting transactions notarised by either a single-node or a distributed notary service (using either the [RAFT consensus algorithm](https://raft.github.io/) or the [BFT-smart consensus algorithm](https://bft-smart.github.io/library/)).

### Features Demonstrated
- Create a custom notary in `MyCustomNotaryService`
- How to edit the configuration files to test with different notaries
### Use Cases
- A notary is required for uniqueness consensus
    - If you were using tokens to represent an asset like a house in a CorDapp that allows you to trade real estate, without a notary guaranteeing the uniqeness of spend, a house owner would be able to sell their house many times over ([double spending](https://www.investopedia.com/terms/d/doublespending.asp)).
- A multi-node notary can be used to provide a highly available notary
    - In a critical business case where the network needs to provide assurance of availability, a multi-node notary can be used to provide fault-tolerance. NOTE: Multi-node notaries are  
### Extending The Sample
- Implement an alternative notary service in one of your CorDapps (or in one of the other sample apps)
- Extend the custom notary to include more features.
### Notes
- Presently, you can only create custom notaries that are single-node.
- Read more about notaries [here](https://docs.corda.net/key-concepts-notaries.html?highlight=notary)
- **WARNING**: Customising a notary service is still an experimental feature and not recommended for most use-cases. The APIs for writing a custom notary may change in the future.
               
 ---
 
 ## [network-verifier](https://github.com/corda/corda/tree/master/samples/network-verifier)
 ### Description
 This CorDapp shows a method of whitelisting participants to add to a flow
 
 
 ### Features Demonstrated
 - Filtering through a list of all nodes registered to the network
 - Filtering using the X500 certificate
 
 ### Use Cases
 
 - Adding users required as participants to a flow
    - There may be situations whereby an entity on the network needs to sign on a transaction. You can use this sample
    as a way of automatically adding that entity as a required participant using their X500 name.
 - Adding users required as observers to a flow
    - Similarly to above, you may not need an entity on the network to act as a required signer, but you may require they be 
    made aware of a transaction. You can use this sample but alter it so that the entity is added as an observer rather than
    a participant.
 ### Extending The Sample
 - Deploy four nodes on the network and create a transaction that requires a third node to act as a participant, and then as an
 observer. You can [query what transactions were made visible to what nodes through the shell](https://docs.corda.net/api-vault-query.html?highlight=query%20vault)
 ### Notes
 - **INCOMPLETE SAMPLE**