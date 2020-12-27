# Blind-Auction-Smart-Contract
A blind auction written in solidity where the winner of the auction is the highest bidder, but only pays the 2nd highest price.


## Functions
```solidity
function bid(bytes32 input) payable PayDeposit public
```
Bidder will send the hash of (public address, value, nonce) to commit his bid. (Bid is available for a certain time frame)  


```solidity
function reveal(uint256 value,bytes32 nonce) payable public 
```
After bidding time comes revealing time (can't bid anymore) and with each reveal the winner gets updated.  


```solidity
function activateWinner() public returns (address)
```
After revealing time is over anyone can call activateWinner which transfers (value of the winner bid - 2nd highest value) back to the winner (as he only pays the 2nd highest price), transfer winner bid to auction manager, transfers any ether stuck in the contract (deposit fees of bidders that didn’t reveal their bids) to the auction manager and returns the address of the winner.  

## Things to note:

* Why include the puplic address to bid value?  
To prevent replay attacks.
If 2 bids have the same value bid the 1st bidder wins.
So if we didn't add public address and an adversarial could somehow intercept your traffic, send your hash(value,nonce) from his address, then allow your hash to pass, then wait for you to reveal the (value,nonce) and do the same thing ( intercept - reveal your (value,nonce) first - allow your traffic to pass), he is  guaranteed to win the bid against you.
By adding public address we can protect against this type of attack.  

* Why does the winner get updated each time someone reveals?  
To change the time complexity, instead of manager doing O(n) operations, each participants will do O(1) operation reducing gas cost on manager.


```solidity
function generateInput(uint256 value,bytes32 nonce) public view returns (bytes32){
    return sha256(abi.encodePacked(msg.sender,value,nonce));
    }
```
* generateInput function, used for testing purposes only. Provide it with the bidding value in wei and nonce and it will calculate the hash value you should provide to the contract. ( This shouldn’t be used in a real environment so you don’t transmit your (value,nonce) publicly )


* Auction manager can edit bidding & revealing time frames and deposit fees before deploying contract. 
