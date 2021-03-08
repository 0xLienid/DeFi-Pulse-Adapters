# DeFi Pulse JSON Configurable Adapter Writing Doc

# Documentation Links

[Follow These Instructions First](https://github.com/ConcourseOpen/DeFi-Pulse-Adapters/blob/master/README.md) <br/>
[SDK Reference](https://github.com/ConcourseOpen/DeFi-Pulse-Adapters/blob/master/docs/sdk.md)

# Writing a JSON Configurable TVL Adapter

Let's take a look at the existing [Loopring](https://github.com/ConcourseOpen/DeFi-Pulse-Adapters/blob/master/v2/projects/loopring) or [Balancer](https://github.com/ConcourseOpen/DeFi-Pulse-Adapters/blob/master/v2/projects/balancer) or [xDai](https://github.com/ConcourseOpen/DeFi-Pulse-Adapters/blob/master/v2/projects/xdai) adapters to see a minimal example of how to write and test a JSON Configurable adapter. Each token adapter gets it's own sub-directory under `/v2/projects`, with an index.js file containing the main json configurations and settings.

```
v2
└───projects
  └───loopring
    └───index.js
```

Feel free to add additional files and folders within your project adapter directly as needed to help organize your code, but most json configurable adapters should be very simple. Please keep all code within your token adapter directory - PR's with modifications or additions outside the scope of your own project usually won't be allowed, and in cases where they are needed will need to be discussed with the DeFi Pulse team in advance.

## Project Metadata
```js
module.exports = {
  name: 'Loopring',         // token project name
  symbol: 'LRC',            // protocol token symbol (if exists any)
  category: 'DEXes',        // allowed values can be 'Derivatives', 'DEXes', 'Lending', 'Payments', 'Assets'
  start: 1514764800,        // unix timestamp (utc 0) specifying when the project began, or where live data begins
  ...
}
```

## The main ```tokenHolderMap``` configurations

The main tokenHolderMap part of the adapter is where you add custom configurations for your adapter. On DeFi Pulse, This tokenHolderMap configuration will be used every hour, with a unix timestamp and block number to automatically fetch token balances locked in your protocol. Please note that project adapters need to be able to run successfully for any point back to a project starting time, not just for recent points. This is necessary both to allow collection of historical data that may exist prior to the release of a newly added project, and for repairing or catching up a projects data history in the event of any errors.

Each item in the tokenHolderMap consists of 2 main parts:

#### ```tokens``` configuration
The tokens property of tokenHolderMap can be a single token, a list of tokens, an executable function that will return a single or a list of tokens, or a json configuration that can be used to pull token information from pool smart contracts.

#### ```holders``` configuration
The holders property of tokenHolderMap can be a single holder/vault/pool address, a list of addresses, an executable function that will return a single or a list of addresses, or a json configuration that can be used to pull pool address from pool smart contracts.

#### ```Loopring``` adapter `tokenHolderMap` configuration
```js
module.exports = {
  ...
  ...
  /* required for indexing token balances */
    tokenHolderMap: [
      {
        tokens: [
          '0xBBbbCA6A901c926F240b89EacB641d8Aec7AEafD',  // LRC
          '0xdac17f958d2ee523a2206206994597c13d831ec7',  // USDT
          '0x6B175474E89094C44Da98b954EedeAC495271d0F',  // DAI
          '0x514910771AF9Ca656af840dff83E8264EcF986CA',  // LINK
          '0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48',  // USDC
          '0x2260FAC5E5542a773Aa44fBCfeDf7C193bc2C599',  // WBTC
          '0x9f8F72aA9304c8B593d555F12eF6589cC3A579A2',  // MKR
        ],
        holders: [
          '0x674bdf20A0F284D710BC40872100128e2d66Bd3f',
          '0x944644Ea989Ec64c2Ab9eF341D383cEf586A5777'
        ],
        checkETHBalance: true,
      }
    ]
}
```

Add ```checkETHBalance: true``` code snippet in case your adapter needs to track ETH balances as well.