Good Reading:
https://redis.io/topics/data-types-intro


To see all of the keys in redis matching a specified pattern (*):

```> KEYS *```

To get the type of any given key:

```> TYPE <key>```

You'' see things like  `set`, or `zset` (sorted set) and hash as the result.

A sorted set is a set sorted by each elements `score` which is a number associated with every element in the set

All the keys in our redis will be in the following form:


***

djac.\<provider\> // **HASH** of company_ids (database primary keys) to its `get_data` dict

***

djac.\<provider\>.es // **SET** of all exact terms

djac.\<provider\>.e.\<exact_term\> // **ZSET** (sorted set) of values associated with that exact term

***

djac.\<provider\>.ps // **SET** of all prefix terms

djac.\<provider\>.p.\<prefix_term\> // **ZSET** (sorted set) of values associated with that prefix term

***

djac.\<provider\>.fm // **HASH** of all facet maps

***

# General Useful Commands
`>>> keys <pattern>` show a list of redis_keys
`>>> type <redis_key>` get the type of this redis_key (hash, set, zset)

# Useful commands for HASH
`>>> hget <redis_key> <hash_key/field>` returns the value of that hash_key/field

`>>> hgetall <redis_key>` returns a list of all fields and values of that hash

# Useful commands for SET
`>>> smembers <redis_key>` returns all members of a set

# Useful commands for ZSET
`>>> zrange <redis_key> <start> <end> WITHSCORES`




With a key of type `zset` you can use the following command to see its value:

```ZRANGE <key> <starting_element> <ending_element>```

```ZRANGE <key> 0 -1``` (0 and -1 will give you all elements) (think python slicing but ending_element is inclusive)

or

```ZRANGE <key> 0 -1 WITHSCORES``` (to get each elements score printed out along with each value in the set)

