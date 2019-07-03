# corda-sample-summaries
A guide to the Corda samples

---

## [Blacklist](https://github.com/corda/samples/tree/release-V4/blacklist)

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

## [Node Info](https://github.com/corda/samples/tree/release-V4/corda-nodeinfo)

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

## [CorDapp-example (IOU)](https://github.com/corda/samples/tree/release-V4/cordapp-example)

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

## [Explicit-cordapp-upgrades](https://github.com/corda/samples/tree/release-V4/explicit-cordapp-upgrades)

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

## [Blacklist](https://github.com/corda/samples/tree/release-V4/blacklist)

### Description

### Features Demonstrated

### Cases

### Extending the Sample

### Notes

---

