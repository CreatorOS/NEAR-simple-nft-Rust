# Creating simple NFTs on NEAR  
In this quest, we will learn how to write a Rust smart contract that will allow us to store information on NEAR. The contract will keep a mapping in its state. This mapping will hold a key-value pair of "who owns this token". So, a mapping from token Ids to NEAR account IDs. Really simple, huh? Let's get to it!

## Before writing the logic
Every contract should start with imports, right?
In our case, we need to import from the NEAR SDK. Take a look at the following lines. These should go on top:
```ts
use near_sdk::borsh::{self, BorshDeserialize, BorshSerialize};
use near_sdk::{near_bindgen, AccountId};
near_sdk::setup_alloc!();
use near_sdk::collections::UnorderedMap;
```
Just boilerplate imports, modules that we are going to use in the contract. See the last line? We are going to use a specialized collection that is optimized to work with NEAR (UnorderedMap). It is an iterable Trie data structure. NEAR developers recommend using NEAR SDK collections instead of Rust standard collections (like HashMap).
We are marching on!
## Writing the contract's state
Let's take our time on this one, so we need to write a contract that keeps an UnorderdMap. But where to write this map? Wait, we need something that can store a state and functions that interact with this state. Maybe a struct?
Above the struct declaration, you can see two weird-looking lines. These lines are required for serialization and deserialization so that the contract is compatible with the NEAR protocol.
There is only one thing for you to do in this subquest: 
STEP 1: Complete the map declaration.
Hint: Remember, we need a mapping from token Ids (strings) to NEAR account IDs. Maybe something from the imports can help?   
```ts
#[near_bindgen]
#[derive(BorshDeserialize, BorshSerialize)]
pub struct NftOwners {
    owners: UnorderedMap<String, /*STEP 1*/>,
}
```
Good job! Let's move on.

## Initializing the state
We need to define the initial value our state takes. Nothing for you to write here, but let's get acquainted with implementing the default behavior for our struct. Nothing really Blockchain-related here maybe, except the "o" prefix you see between parentheses. This is a storage prefix that should be kept unique compared with other prefixes in your code to avoid collisions. 
```ts
impl Default for NftOwners {
    fn default() -> Self {
        Self {
            owners: UnorderedMap::new(b"o"),
        }
    }
}
```
## writing functions - setter
Alright, now we have to write our functions. But there is one thing before this, all functions should go inside the brackets in this statement:
```ts
#[near_bindgen]
impl NftOwners {

}
```
Take a look at the set_owner function, there is one thing left to do. 
STEP 1: write the right key to be inserted into the mapping, it is in the parameter list.
```ts
 pub fn set_owner(&mut self, token_id: String, account_id: AccountId) {
        self.owners.insert(&/*STEP 1*/, &account_id);
    }
```
We also need a getter function, let's write it then!

## Writing functions - getter 
So this getter takes in a token ID and returns an account id. It matches the value associated with this key (token_id in the code). If found, then it returns it (owner in the code). Otherwise, returns an error message.
STEP 1: Pass the proper key to ```ts get()```. Remember, include the "&" before passing the argument. 
```ts
 pub fn get_owner(&self, token_id: String) -> AccountId {
        match self.owners.get(/*STEP 1*/) {
            Some(owner) => owner,
            None => "No owner found".to_string(),
        }
    }
```
And yes, that is it! Stay tuned and Happy Coding!