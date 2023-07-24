I audited the `VaultController` contract and checked each function's code with focus and the main vulnerabilities I found in this protocol was Precision Scaling and math used to calculate borrowing power fees and liqudation process. I realized in functions that are dealing with different tokens, the token's precision is not scaled and it's is considerd all of them to have 18 decimals and as I explained with details in my findings this can cause lots dangerous problems.

In some of the fee calculations I didn't know what and how much is expected to be returned from the calculation so I couldn't say there is somthing wrong, I recommend your team to do some more research and review math and fees calculation in case if wardens missed some vulnerabilities.

### Time spent:
12 hours