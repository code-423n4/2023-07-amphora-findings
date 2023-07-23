## [L-01] SWC-120 Weak Sources of Randomness from Chain Attributes.  Use of block number as source of randomness.

# Vulnerable URLS
```url
https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L170

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L195

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L196

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L209

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L210

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L218

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L219

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L371

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L375

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L467

https://github.com/code-423n4/2023-07-amphora/blob/daae020331404647c661ab534d20093c875483e1/core/solidity/contracts/governance/GovernorCharlie.sol#L468
```

## Relationships
```txt
CWE-330: Use of Insufficiently Random Values
```

## Description
```txt
Ability to generate random numbers is very helpful in all kinds of applications. One obvious example is gambling DApps, where pseudo-random number generator is used to pick the winner. However, creating a strong enough source of randomness in Ethereum is very challenging. For example, use of block.timestamp is insecure, as a miner can choose to provide any timestamp within a few seconds and still get his block accepted by others. Use of blockhash, block.difficulty and other fields is also insecure, as they're controlled by the miner. If the stakes are high, the miner can mine lots of blocks in a short time by renting hardware, pick the block that has required block hash for him to win, and drop all others.
```

## POC
# Vulnerable code
```sol
// ln 170
if (amph.getPriorVotes(msg.sender, (block.number - 1)) < proposalThreshold && !isWhitelisted(msg.sender)) {
      revert GovernorCharlie_VotesBelowThreshold();

// ln 195
startBlock: block.number + votingDelay,

// ln 196
endBlock: block.number + votingDelay + votingPeriod,

// lns 209-210
_newProposal.startBlock = block.number;
      _newProposal.endBlock = block.number + emergencyVotingPeriod;

// lns 218-219
 _newProposal.startBlock = block.number + optimisticVotingDelay;
      _newProposal.endBlock = block.number + optimisticVotingDelay + votingPeriod;

// lns 371-375
 if (
          (amph.getPriorVotes(_proposal.proposer, (block.number - 1)) >= proposalThreshold)
            || msg.sender != whitelistGuardian
        ) revert GovernorCharlie_WhitelistedProposer();
      } else {
        if ((amph.getPriorVotes(_proposal.proposer, (block.number - 1)) >= proposalThreshold)) {
          revert GovernorCharlie_ProposalAboveThreshold();
        }

// lns 467-468
else if (block.number <= _proposal.startBlock) return ProposalState.Pending;
    else if (block.number <= _proposal.endBlock) return ProposalState.Active;

```

## Remediation
```txt
Using commitment scheme, e.g. RANDAO.
Using external sources of randomness via oracles, e.g. Oraclize. Note that this approach requires trusting in oracle, thus it may be reasonable to use multiple oracles.
Using Bitcoin block hashes, as they are more expensive to mine.
```