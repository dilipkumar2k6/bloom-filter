# Summary
- Lookup if a given key exist in list or not
![](assets/lookup-service.png)
- Check if CAR present in the list
- Check if CAR not present in the list
For example in signup service to lookup username
- Check if username already taken or not
# Alternate approach
## Use list of keys
- Maintain the list of keys
- For new key, check if it does exist or not in the list
Cons:
- Iterate each key and compare. 
- This is not optimal time complexity 
- Not flexible to use with distributed system
## Hash table
- Manage all keys in hash
- Check if given key exist in hash table
Cons:
- Time complexity is not always O(1) in case of hash collision
- Hash table size will keep growing
- Need to manage distributed hash table
- Distributed hash table means storing it on disk which add latency
# Bloom filter
- This is probabilistic data structure
- This is extension of hashing
- This only answer `not present` with `100%` guarantee 
- This answer `present` with `90%` guarantee only
Pros:
- Take lot less storage
- It is easy to store in memory to perform operation
# Design Bloom filter
## Build Bloom filter
- Take bit array initialize as `0`
- Tak two hash function (we can take more than two as well)
- Run hash function on each word
Note: More has functions mean less collision i.e `K` number of hash functions
- Time complexity is `O(k)`
```
hash1(CAT) = 2
hash2(CAT) = 6
 ```
 ```
 hash1(DOG) = 4
 hash2(DOG) = 10
 ```
 ```
 hash1(RAT) = 10
 hash2(RAT) = 19
 ```
 - Set respective index to `1`
 - If existing bit is already `1` then don't do anything
## Query key
### Check if `DOG` exists?
```
hash1(DOG) = 4
hash(DOG) = 10
```
- If both place `4` and `10` is set to `1` then `DOG` does exists
### Check if `COW` exists?
```
hash1(COW) = 19
hash2(COW) = 23
```
- check bit position for `19` and `23`
- since both are not present therefore `COW` doesn't exist
###  Check if `HEN` exists?
```
hash1(HEN) = 19
hash2(HEN) = 2
```
- check bit position of `19` and `2`
- both bits are set
- but `HEN` doesn't exist
- That's why, when bloom filter says key present, it is not `100%` confidant
- When it says word doesn't exist then it is `100%` confidant 

# Optimized Bloom filter
- Instead of taking bit array, take number and its bits representation
- `000111000110001110000111000001110001` 
- Save corresponding decimal number in storage
# Considerations for Bloom filter
- For big number of words list, need to pick big bit array
- For `40 millions` of records
    - 159 mb of storage for bloom filter
    - hash(k) k  = 23
    - probability mistake = 1 mistake in 10 million query
# Applications of bloom filter
## Database
`SELECT * FROM TABLE USER WHERE USERID=1000`
- Database store all userids as bloom filter
- it first check the presence of userid `10000`
- if doesn't exist then confidently return no data
- if exist then run query to read from disk
Following database uses bloom filter for query optimization
- HBase
- Cassandra
- Postgress
## Chrome browser malicious url check
- Google manages list of malicious urls on its server and keep updating it regularly
- Lit of these size is around `50MB`
- When user browse url in chrome browser, chrome browser needs to check if url is malicious or not
- They can't keep getting updated list of malicious urls from server on every days/weeks/minute/seconds
How does it work?
- Google generate bloom filter for list of malicious urls
- Google sends bloom filter hash of size 1MB to all browser
- Browser already knows the list of hash function used to calculate the bloom filter
- Browser creates hash for given url and check against the bloom filter
## Medium.com to check on story recommendations
- It maintains bloom filter of each and every users
- For new story, it checks into bloom filter for user and see if story is already recommended or not
## Bitcoin
- Use to synchronize the wallet  

# Reference 
https://www.youtube.com/watch?v=Bay3X9PAX5k&ab_channel=TechDummies