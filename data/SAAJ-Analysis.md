# Approach
The approach i   taken for Amphora contest is first going through the documentation and understand all the operational information provided that which get me to importance of ‘Governer Charlie contract’ that controls the overall governance of the protocol.
Then i go through math model of Interest protocol that provides me some idea on how liquidity and interest rate is calculated for the protocol.
Then i started taking a line-by-line approach for reviewing all the contracts in scope first for optimisation and low severity issues.
After getting over Gas and QA i went for Medium and high severity findings by taking a more deep approach by looking at every function with also reading past audit reports  on ‘Solodit’ for identifying  similar bugs or pattern that may provide context to potential attack vectors.

# Learning
My learning from this project is to identify bugs and issues based on similar patterns that have lead to exploitation for different protocols. Solodit in this regard provide a comprehensive study guide which,  i will from now on will use for every audit contest.

# Comments
My findings are related to attack vectors that arise due to misuse of minting functions and cross chain operational issues that can lead to loss of funds for users as well as the project itself.
The findings related to mint, deposit and withdraw functions focus on loss to user for the amount they intended to receive but a malicious attacker can get them before they do.
Cross chain issue is related to usage of block.number than can create problem in terms of using contract on different chain that can cause problem in implementing conditions according to requirement.



### Time spent:
35 hours