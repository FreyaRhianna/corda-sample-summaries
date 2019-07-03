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

