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

## [Blacklist](https://github.com/corda/samples/tree/release-V4/blacklist)

### Description

### Features Demonstrated

### Cases

### Extending the Sample

### Notes

---

## [Blacklist](https://github.com/corda/samples/tree/release-V4/blacklist)

### Description

### Features Demonstrated

### Cases

### Extending the Sample

### Notes

---

## [Blacklist](https://github.com/corda/samples/tree/release-V4/blacklist)

### Description

### Features Demonstrated

### Cases

### Extending the Sample

### Notes

---

## [Blacklist](https://github.com/corda/samples/tree/release-V4/blacklist)

### Description

### Features Demonstrated

### Cases

### Extending the Sample

### Notes

---

