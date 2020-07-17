Provides the off-chain rate between two tokens

The price oracle uses chainlink oracles internally to get the rates of the two tokens

with respect to a common denominator, and then returns the rate between them, which

is equivalent to the rate of TokenA / TokenB

# Functions:

- [`constructor(contract IERC20Token _tokenA, contract IERC20Token _tokenB, contract IChainlinkPriceOracle _tokenAOracle, contract IChainlinkPriceOracle _tokenBOracle)`](#PriceOracle-constructor-contract-IERC20Token-contract-IERC20Token-contract-IChainlinkPriceOracle-contract-IChainlinkPriceOracle-)

- [`latestRate(contract IERC20Token _tokenA, contract IERC20Token _tokenB)`](#PriceOracle-latestRate-contract-IERC20Token-contract-IERC20Token-)

- [`lastUpdateTime()`](#PriceOracle-lastUpdateTime--)

- [`latestRateAndUpdateTime(contract IERC20Token _tokenA, contract IERC20Token _tokenB)`](#PriceOracle-latestRateAndUpdateTime-contract-IERC20Token-contract-IERC20Token-)

# Function `constructor(contract IERC20Token _tokenA, contract IERC20Token _tokenB, contract IChainlinkPriceOracle _tokenAOracle, contract IChainlinkPriceOracle _tokenBOracle)` {#PriceOracle-constructor-contract-IERC20Token-contract-IERC20Token-contract-IChainlinkPriceOracle-contract-IChainlinkPriceOracle-}

initializes a new PriceOracle instance

note that the oracles must have the same common denominator (USD, ETH etc.)

## Parameters:

- `_tokenA`:         first token to support

- `_tokenB`:         second token to support

- `_tokenAOracle`:   first token price oracle

- `_tokenBOracle`:   second token price oracle

# Function `latestRate(contract IERC20Token _tokenA, contract IERC20Token _tokenB) → uint256, uint256` {#PriceOracle-latestRate-contract-IERC20Token-contract-IERC20Token-}

returns the latest known rate between the two given tokens

for a given pair of tokens A and B, returns the rate of A / B

(the number of B units equivalent to a single A unit)

the rate is returned as a fraction (numerator / denominator) for accuracy

## Parameters:

- `_tokenA`: token to get the rate of 1 unit of

- `_tokenB`: token to get the rate of 1 `_tokenA` against

# Function `lastUpdateTime() → uint256` {#PriceOracle-lastUpdateTime--}

returns the timestamp of the last price update the rates are returned as numerator (token1) and denominator

(token2) for accuracy

# Function `latestRateAndUpdateTime(contract IERC20Token _tokenA, contract IERC20Token _tokenB) → uint256, uint256, uint256` {#PriceOracle-latestRateAndUpdateTime-contract-IERC20Token-contract-IERC20Token-}

returns both the rate and the timestamp of the last update in a single call (gas optimization)

## Parameters:

- `_tokenA`: token to get the rate of 1 unit of

- `_tokenB`: token to get the rate of 1 `_tokenA` against

## Return Values:

- timestamp of the last update