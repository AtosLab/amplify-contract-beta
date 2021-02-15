# amplify-protocol

AMPLIFY - Decentralized Financial Infrastructure

# Developmeny Environment
* solidity ^0.5.16
* nodejs 12+

# Installion
```
yarn
```

# Compile
```
npx saddle compile
```

# Deploy（Rinkeb for example）

## 1. Environment Prepare

Create a .env file in the project directory as follows
```
ACCOUNT={Ethereum private key}
```
The address of the private key of Ethereum needs to have eth token on the network as the gas fee.



Take rinkeby network as example. create `~/.ethereum/rinkeby` file with Ethereum private key as content.


## 2. Deploy FaucetToken contracts

Deploy WTBC for testnet

```shell    
# Deploy
npx saddle deploy FaucetToken 100000000000000000000000000 "WBTC" 18 "WBTC"  -n rinkeby

# Verify on Etherscan.io上
npx saddle verify "{Etherscan Api Key}" "{Deployed WBTC contract address}" FaucetToken 100000000000000000000000000 "WBTC" 18 "WBTC" -n rinkeby
```

Deploy USDT for testnet

```shell
# Deploy
npx saddle deploy FaucetToken 100000000000000000000000000 "USDT" 18 "USDT"  -n rinkeby

# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed USDT contract address}" FaucetToken 100000000000000000000000000 "USDT" 18 "USDT" -n rinkeby
```

Deploy USDC for testnet
```shell
# Deploy
npx saddle deploy FaucetToken 100000000000000000000000000 "USDC" 18 "USDC"  -n rinkeby

# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed USDC contract address}" FaucetToken 100000000000000000000000000 "USDC" 18 "USDC" -n rinkeby
```

Deploy DAI for testnet
```shell
# Deploy
npx saddle deploy FaucetToken 100000000000000000000000000 "DAI" 18 "DAI"  -n rinkeby

# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed DAI contract address}" FaucetToken 100000000000000000000000000 "DAI" 18 "DAI" -n rinkeby
```

Deploy AMPT for testnet

```shell
# Deploy
npx saddle deploy AMPT "{Initial token recipient address}"  -n rinkeby

# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed AMPT contract address}" AMPT "{Initial token recipient address}"  -n rinkeby
```

## 3. Deploy Comptroller Contract

 Update Comptroller.sol

```javascript
/**
* @notice Return the address of the AMPT token
* @return The address of AMPT
*/
function getCompAddress() public view returns (address) {
    return /*{AMPT token contract address}*/;
}
```

Deploy contract
```shell
# Deploy
npx saddle deploy Comptroller -n rinkeby

# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed Comptroller contract address}" Comptroller -n rinkeby
```


## 4. Deploy InterestRate Contract

InterestRate contract is the implementation of interest rate calculation model

For example: the base interest rate is 5%, and the multipler interest rate is 12%

Deploy Contract
```shell
# Deploy
npx saddle deploy WhitePaperInterestRateModel 50000000000000000 120000000000000000 -n rinkeby
# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed InterestRate contract address}" WhitePaperInterestRateModel 50000000000000000 120000000000000000 -n rinkeby
```

## 5. Deploy cTokens contract
Ctoken is the proof of deposit.

Deploy cETH Contract
```shell
npx saddle deploy CEther "{Deployed Comptroller contract address}" "{Deployed InterestRate contract address}" "2000000000000000000" "AMPLIFY cETH" "cETH" "8" "{Administrator address}" -n rinkeby
```

Deploy cWBTC Contract
```shell
npx saddle -n rinkeby script token:deploy '{
  "underlying": "{wBTC token contract address}",
  "comptroller": "{Deployed Comptroller contract address}",
  "interestRateModel": "{Deployed InterestRate contract address}",
  "initialExchangeRateMantissa": "2.0e18",
  "name": "AMPLIFY WBTC",
  "symbol": "cWBTC",
  "decimals": "8",
  "admin": "{Administrator address}"
}'
```

Deploy cUSDT Contract
```shell
npx saddle -n rinkeby script token:deploy '{
  "underlying": "{USDT token contract address}",
  "comptroller": "{Deployed Comptroller contract address}",
  "interestRateModel": "{Deployed InterestRate contract address}",
  "initialExchangeRateMantissa": "2.0e18",
  "name": "AMPLIFY USDT",
  "symbol": "cUSDT",
  "decimals": "8",
  "admin": "{Administrator address}"
}'
```

Deploy cUSDC Contract
```shell
npx saddle -n rinkeby script token:deploy '{
  "underlying": "{USDC token contract address}",
  "comptroller": "{Deployed Comptroller contract address}",
  "interestRateModel": "{Deployed InterestRate contract address}",
  "initialExchangeRateMantissa": "2.0e18",
  "name": "AMPLIFY USDC",
  "symbol": "cUSDC",
  "decimals": "8",
  "admin": "{Administrator address}"
}'
```

Deploy cDAI Contract
```shell
npx saddle -n rinkeby script token:deploy '{
  "underlying": "{DAI token contract address}",
  "comptroller": "{Deployed Comptroller contract address}",
  "interestRateModel": "{Deployed InterestRate contract address}",
  "initialExchangeRateMantissa": "2.0e18",
  "name": "AMPLIFY DAI",
  "symbol": "cDAI",
  "decimals": "8",
  "admin": "{Administrator address}"
}'
```

Deploy cAMPT Contract
```shell
npx saddle -n rinkeby script token:deploy '{
  "underlying": "{AMPT token contract address}",
  "comptroller": "{Deployed Comptroller contract address}",
  "interestRateModel": "{Deployed InterestRate contract address}",
  "initialExchangeRateMantissa": "2.0e18",
  "name": "AMPLIFY AMPT",
  "symbol": "cAMPT",
  "decimals": "8",
  "admin": "{Administrator address}"
}'
```

## 6. Configure Comptroller
Send transaction via Etherscan.io to call the contract method.

call _setMaxAsset to set the value of maxAssets to 20

call _supportMarket method to add cTokens to the token list. Each cToken needs to be added.

call _setCollateralFactor method to set the collateral factor of cToken.  Each cToken needs to be set.

## 6. Deploy PriceOracle
We use the Oracle anchored by uniswap in mainnet, first obtain the token price from coinbase or other exchanges, submit it to the smart contract, and use the price of uniswap as the verification.

See in the project: amplify-open-oracle

## 7. Deploy SimplePriceOracle
Simpleprice Oracle can be released for testing. It can be launched in the early stage in order to avoid Oracle attack.

```shell
# Deploy
npx saddle deploy SimplePriceOracle -n rinkeby
# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed SimplePriceOracle Contract address}"  SimplePriceOracle -n rinkeby
```

## 8. Configure oracle address of the Comptroller.

Send transaction via Etherscan.io to call the contract method.

call _setPriceOracle to set the oracle contract address of the Comptroller


## 9. Deploy CompoundLens
CompoundLens is used for data calculation and query
```shell
# Deploy
npx saddle deploy CompoundLens -n rinkeby
# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed CompoundLens Contract address}" CompoundLens -n rinkeby
```

## 10. Deploy Maximillion
```shell
# Deploy
npx saddle deploy Maximillion "{Deployed cETH contract address}" -n rinkeby
# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed Maximillion contract address}" Maximillion "{deploy cETH address}" -n rinkeby

```


## 11. Deploy Timelock Contract
Timelock is the contract for decentralized governance
```shell
# Deploy
npx saddle deploy Timelock "{Administrator address}" 600 -n rinkeby
# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed Timelock Contract address}" Timelock "{Administrator address}" 600 -n rinkeby
```

## 12. Deploy GvernorAlpha Contract
GvernorAlpha is the contract of vote.
```shell
# Deploy
npx saddle deploy GovernorAlpha "{Deployed Timelock Contract address}" "{AMPT token contract address}" "{Operator address}"  -n rinkeby
# Verify on Etherscan.io
npx saddle verify "{Etherscan Api Key}" "{Deployed GvernorAlpha Contract address}" GovernorAlpha "{Timelock contract address}" "{AMPT token contract address}" "{Operator address}"  -n rinkeby
```


## 13. Set Timelock as the administrator of each governed contract

1）In each governed contract, call `_setPendingAdmin` method with the old administrator, set the timelock contract as `pendingAdmin`.

2）By calling `queueTransaction` and `executeTransaction` methods of timelock, let timelock contract invoke `_acceptAdmin` method of  governed contracts, so that it can be new administrator of governed contracts

## 14. Set  `GovernorAlpha` as the administrator of Timelock

1）Use `queueTransaction` and `executetransaction` methods of the Timelock contract to invoke TimeLock contract's `setPendingAdmin` method

2) Invoke `_acceptAdmin` method of `GovernorAlpha` contract so that it will be admin for TimeLock contract