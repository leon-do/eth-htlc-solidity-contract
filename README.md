```
pragma solidity ^0.4.0; 

contract HTLC {

    /* contract starts now */
    uint public startTime = now;
    
    /* contract expires in 60 */
    uint public lockTime = 60 seconds;
    
    /* send ETH toAddress */
    address public toAddress = 0x32D254039DF203D4C6CD645F02f3403Ef605Cc79;
    
    /* the key required to spend the bitcoin transaction example: 0xa492d599fb01a1801f3b810b0f8fd8e3efe9725ecbbe43e7341333c86407fab7 */
    bytes32 public key; 
    
    /* sha256('0xa492d599fb01a1801f3b810b0f8fd8e3efe9725ecbbe43e7341333c86407fab7') */
    bytes32 public hash = 0x829737b3a9e8f4f58167ff3105d211dedfec64cddfc091444283592b9dfdac12; 
    
    /* return address in case the contract times out */
    address public fromAddress; 
    
    /* the amount in the contract */
    uint public fromValue; 

    function HTLC() payable { 
        fromAddress = msg.sender; 
        fromValue = msg.value; 
    } 

    modifier condition(bool _condition) { 
        require(_condition); 
        _; 
    } 

    /* if hashing the key matches, then transfer ETH toAddress */
    function withdraw (bytes32 _key) payable condition ( sha256(_key) == hash ) { 
        key = _key; 
        toAddress.transfer(fromValue); 
    } 

    /* if the contract times out, then return the funds */
    function refund () payable condition ( now > startTime + lockTime ) returns (uint) { 
        fromAddress.transfer(fromValue); 
        return fromValue; 
    } 

}
```
