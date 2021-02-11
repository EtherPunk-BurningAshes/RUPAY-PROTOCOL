# 🍠  RUPAY Protocol  🍠
## The Protocol
RUPAY is an experimental protocol building upon the most exciting innovations in programmable money and forked from YAM PROTOCOL.

At its core, RUPAY is an elastic supply cryptocurrency, which expands and contracts its supply in response to market conditions, initially targeting 1 RUPEES per RPY. This stability mechanism includes one key addition to existing elastic supply models such as Ampleforth: a portion of each supply expansion is used to buy yCurve (a high-yield USD-denominated stablecoin) and add it to the RUPAY treasury.

## The Token
The core RPY token uses yCRV as the reserve currency, which is roughly a $1 peg. Each supply expansion (referred to as an inflating rebase), a portion of tokens is minted and used to build up the treasury. 

## Rebases

Rebases are controlled by an external contract called the Rebaser. This is comparable to Ampleforth's `monetaryPolicy` contract. It dictates how large the rebase is and what happens on the rebase. The YAM token just changes the supply based on what this contract provides it.

There are a requirements before rebases are active:
<br />
•	Liquid YAM/yCRV market<br/>
•	`init_twap()`<br/>
•	`activate_rebasing()`<br/>

Following the launch of the second pool, rebasing can begin its activation phase. This begins with `init_twap()` on the rebaser contract. Anyone can call this at anytime once there is a YAM/yCRV Uniswap V2 market. The oracle is designed to be 12 hours between checkpoints. Given that, 12 hours after `init_twap()` is called, anyone can call `activate_rebasing()`. This turns rebasing on, permanently. Now anyone can call `rebase()` when `inRebaseWindow() == true;`.

In a rebase, the order of operations are:
<br />
•	ensure in rebase window<br/>
•	calculate how far off-price is from the peg<br/>
•	dampen the rebase by the rebaseLag<br/>
•	if positive calculate protocol mint amount<br/>
•	change scaling factor, (in/de)flating the supply<br/>
•	sync uniswap, mint, sell to uniswap, transfer excess YAM and bought yCRV to reserves<br/>
•	call any extra functions governance adds in the future (i.e. Balancer gulps)<br/>

# Development
### Building
This repo uses truffle. Ensure that you have truffle installed. Given the composability aspect of this

Then, to build the contracts run:
```
$ truffle compile
```

To run tests, run against a single test package, i.e.:
```
$ sh startBlockchain.sh
$ truffle migrate --network distribution
$ python scripts/clean.py
$ cd jsLib
$ jest deployment
$ jest token
$ jest rebase
```
The need to run one-by-one seems to be a limitation of jest + ganache.

The distribution tests require specific tokens. These are acquired by using the ganache unlock_account function. If you receive fails, the owner likely decreased their ownership of that token. Just replace any instances of that address with another holder of the token.

Note: some governance tests require a different ganache setup. You will encounter a warning (but not a failed test) if the wrong type of ganache is setup. To run the correct one:
```
$ sh startBlockchainMining.sh
$ truffle migrate --network distribution
$ python scripts/clean.py
$ cd jsLib
$ jest governance
```


#### Attributions
Much of this codebase is modified from existing works, including:

[YAM](https://yam.finance) 
