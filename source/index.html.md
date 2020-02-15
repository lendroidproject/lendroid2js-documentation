---
title: Lendroid-2 Library

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript

toc_footers:
  - <a href='https://github.com/lendroidproject/lendroid-2-js'>Lendroid-2-JS on Github</a>
  - <a href='https://github.com/lendroidproject/wallet-ui'>Wallet UI with Lendorid-2-JS</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Welcome

Welcome to the `Lendroid-2-JS`!

`Lendroid-2-JS` is a library to wrapper the lendroid Server and Lendroid Contract API's.<br/> You can view code examples
in the dark area to the right.

<mark class="notice">It uses web3.js [latest](https://web3js.readthedocs.io/en/v1.2.4/)</mark>

# Installation

> To install:

```shell
yarn add lendroid-protocol
```

You can install `Lendroid-2-JS` using [Yarn](https://yarnpkg.com).

<mark class="notice">
For the specific version, please check it's [releases](https://github.com/lendroidproject/lendroid-2-js/releases) on Github
</mark>

# How to Start ?

## Import

> To import:

```javascript
import { Lendroid } from 'lendroid-protocol'
```

First, you need to Import `Lendroid-2-JS` library.

## Initialize

> To initialize:

```javascript
const options = {
  type: 'metamask',
  tokens,
  onEvent: handleMessage,
}

const LendroidJS = new Lendroid(options)
```

| Key     | Type       | Default    | Description                                                                                                    |
| ------- | ---------- | ---------- | -------------------------------------------------------------------------------------------------------------- |
| type    | `string`   | `metamask` | Provider Type. (metamask, formtatic)                                                                           |
| tokens  | `object`   | -          | Deployed tokens. [example](https://github.com/lendroidproject/wallet-ui/blob/master/assets/contracts.js)       |
| onEvent | `function` | -          | Event listener. [Events](https://github.com/lendroidproject/lendroid-2-js/blob/master/src/constants/Events.ts) |

# What's new ?

## 1. Multiple Providers

`Lendroid-2-JS` working with Metamask as well as [Fortmatic](https://fortmatic.com/) now.

## 2. Event handler

> Example:

```javascript
this.onEvent(Events.NETWORK_CHANGED)
this.onEvent(Events.BALANCE_UPDATED, { data })
this.onEvent(Events.BALANCE_FAILED, err)
```

| Key    | Type     | Default | Description    |
| ------ | -------- | ------- | -------------- |
| event  | `string` | -       | Event name.    |
| params | `object` | -       | Handle params. |

## 3. Dealing promise

Every public methods are returning Promise which can be managed by UI.

# Properties

## web3Utils

Provide [Web3Utils](#web3utils-class).

## contracts

> Example:

```javascript
{
  contracts: {
    Protocol: PROTOCOL_CONTRACT,
    TOKEN_1_SYMBOL: TOKEN_1_CONTRACT,
    TOKEN_2_SYMBOL: TOKEN_2_CONTRACT,
    // ... other token contract
  },
  balances: {
    ETH: ETH_BALANCE,
    LST: LST_BALANCE,
    WETH: WETH_BALANCE,
    TOKEN_1_SYMBOL: TOKEN_1_BALANCE,
    TOKEN_2_SYMBOL: TOKEN_2_BALANCE,
    // ... other token balances
  },
  allowances: {
    LST: LST_ALLOWANCE,
    WETH: WETH_ALLOWANCE,
    TOKEN_1_SYMBOL: TOKEN_1_ALLOWANCE,
    TOKEN_2_SYMBOL: TOKEN_2_ALLOWANCE,
    // ... other token allowances
  },
  positions: {
    lent: [...],
    borrowed: [...]
  },
}
```

<code class="sample">const { contracts } = library</code>

Fetch Contracts, Blance, LSFUI Tokens, Pools and Names, Positions. And provide interface for Contract functions.

| Key           | Type     | Default | Description                    |
| ------------- | -------- | ------- | ------------------------------ |
| contracts     | `object` | `{}`    | Token Contract Instances.      |
| expiries      | `array`  | -       | Registered expiries.           |
| supportTokens | `object` | -       | Contract addresses by Network. |

### Contract Instances

[Web3 Contract Instances](https://web3js.readthedocs.io/en/v1.2.4/web3-eth-contract.html#eth-contract) of Supported
Tokens. Check
[here](https://github.com/lendroidproject/Lendroid-2-JS/blob/master/src/constants/contracts/ContractAddresses.ts) for
supported Token from `Lendroid-2-JS`.

Note: You can define undeployed ABIs
[here](https://github.com/lendroidproject/lendroid-2-js/blob/master/src/constants/SupportTokens.ts). And set `all`
inside of supportTokens - check
[example](https://github.com/lendroidproject/lendroid-2-js/blob/master/src/constants/SupportTokens.ts#L6).

You need to provide contract tokens from `Wallet`. Check example
[here](https://github.com/lendroidproject/wallet-ui/blob/master/assets/contracts.js).

## contracts.expiries

<code class="sample">const { expiries } = library.contracts</code>

Every last `thursday` of month for current year. And manual expiries. You can check how to calculate from
[here](https://github.com/lendroidproject/lendroid-2-js/blob/master/src/services/contracts.ts)

| Key       | Type     | Default | Description               |
| --------- | -------- | ------- | ------------------------- |
| name      | `string` | -       | Expiry name               |
| timestamp | `number` | -       | Unix timestamp of expiry. |
| fullName  | `string` | -       | E.g - `Feb 2020`          |
| date      | `string` | -       | E.g - `2020-02-27`        |

# Methods

## enable

> Sample Code

```javascript
// Metamask
this.library.enable(window.ethereum, 'metamask')

// Fortmatic
const Fortmatic = require('fortmatic')
const fm = new Fortmatic(process.env.FORTMATIC_API_KEY)
this.library.enable(fm.getProvider(), 'fortmatic')
```

<code class="sample">library.enable(provider, type)</code>

Enable provider for the app and initilize [contracts](#contracts)

### Parameters

| Key      | Type       | Default | Description            |
| -------- | ---------- | ------- | ---------------------- |
| postData | `object`   | -       | Data to create Orders. |
| callback | `function` | -       | Callback Function      |

<mark class="notice">
  Be sure `postData` will be matched to your order model on your API.
</mark>

## contracts.constructor

- Initialise [expiries](#contracts-expiries)
- Initialize Tokens (supportTokens, fetchTokens, balanceTokens)
- web3Utils, onEvent, address import
- Fetch contracts and intialize Protocol
- Start balance interval

## contracts.onNetworkChanged

Watch network id and re-initialize protocol

## contracts.onUpdateAddress

Update address and fetch pools and positions

## contracts.getBalances

> Sample Code

```javascript
library.contracts
  .onSplit(token, {
    amount,
    expiry,
    underlying: type ? underlying : undefined,
    strike: type ? Number(strike) : undefined,
  })
  .then(() => {
    library.contracts.getBalances(true)
    callback && callback()
  })
```

<code class="sample">library.contracts.getBalances(true)</code>

Manual method to fetch balances. Also dealing with fetching new contracts.

## contracts.getPoolNames

> Sample Code

```javascript
library.contracts.onRegisterPoolName(poolName).then(() => {
  setSuccess(true)
  library.contracts.getPoolNames()
})
```

<code class="sample">library.contracts.getPoolNames()</code>

Manual method to fetch `pool names`.

## contracts.getRiskFreePools

> Sample Code

```javascript
library.contracts.onPurchase(id, type, marketInfo).then(() => {
  library.contracts.getRiskFreePools()
})
```

<code class="sample">library.contracts.getRiskFreePools()</code>

Manual method to fetch pools (`risk-free`).

## contracts.getRiskyPools

> Sample Code

```javascript
library.contracts.onPurchase(id, type, marketInfo).then(() => {
  library.contracts.getRiskyPools()
})
```

<code class="sample">library.contracts.getRiskyPools()</code>

Manual method to fetch pools (`risky`).

## contracts.getPositions

> Sample Code

```javascript
library.contracts
  .onAvailLoan({ ...form, strike })
  .then(() => {
    console.log('Loan Created')
    library.contracts.getPositions()
  })
  .finally(() => setForm(defaults))
```

<code class="sample">library.contracts.getPositions()</code>

Manual method to fetch `positions`.

# Contract Methods

## contracts.onWrap

> Sample Code

```javascript
library.contracts.onWrap(token, amount).then(() => {
  library.contracts.getBalances()
})
```

<code class="sample">library.contracts.onWrap(token, amount)</code>

Wrap `Token` into `L-Token`.

## contracts.onUnwrap

> Sample Code

```javascript
library.contracts.onUnwrap(token, amount).then(() => {
  library.contracts.getBalances()
})
```

<code class="sample">library.contracts.onUnwrap(token, amount)</code>

Unwrap `L-Token` into `Token`.

## contracts.onSplit

> Sample Code

```javascript
library.contracts
  .onSplit(token, {
    amount,
    expiry,
    underlying: type ? underlying : undefined,
    strike: type ? Number(strike) : undefined,
  })
  .then(() => {
    library.contracts.getBalances(true)
  })
```

<code class="sample">library.contracts.onSplit(token, form)</code>

Split token into `I+F` or `I+S+U` tokens. It will create new IFSU tokens, so `withContract` parameter should be `true`
for `getBalances` method.

## contracts.onFuse

> Sample Code

```javascript
library.contracts
  .onFuse(token, {
    amount,
    expiry,
    underlying,
    strike: Number(strike),
  })
  .then(() => {
    library.contracts.getBalances()
  })
```

<code class="sample">library.contracts.onFuse(token, form)</code>

Fuse split tokens back to wrapped tokens.

## contracts.onRegisterLookUpStake

> Sample Code

```javascript
const handlePoolName = e => {
  const val = e ? e.target.value : ''
  setPoolName(val)
  throttleFunction(len => library.contracts.onRegisterLookUpStake(len).then(handleStake), 200, val.length)
}
```

<code class="sample">library.contracts.onRegisterLookUpStake(poolNameLength)</code>

Look up stack value for `pool name` register by length of name.

## contracts.onRegisterPoolName

> Sample Code

```javascript
library.contracts.onRegisterPoolName(poolName).then(() => {
  library.contracts.getPoolNames()
})
```

<code class="sample">library.contracts.onRegisterPoolName(poolName)</code>

Register `pool name`.

## contracts.onCreatePool

> Sample Code

```javascript
library.contracts.onCreatePool(form).then(() => {
  if (riskFree) {
    library.contracts.getRiskFreePools()
  } else {
    library.contracts.getRiskyPools()
  }
})
```

<code class="sample">library.contracts.onCreatePool(form)</code>

Create a new `Pool`.

## contracts.onOfferNewToken

> Sample Code

```javascript
library.contracts
  .onOfferNewToken(poolId, {
    expiry,
    underlying,
    strike: Number(strike),
    iCostPerDay,
    sCostPerDay,
  })
  .then(() => {
    fetchPools()
  })
```

<code class="sample">library.contracts.onOfferNewToken(poolId, form)</code>

Offer new token to Pool.

## contracts.onWithdrawEarnings

> Sample Code

```javascript
library.contracts.onWithdrawEarnings(poolId, riskFree).then(() => {
  fetchPools()
})
```

<code class="sample">library.contracts.onWithdrawEarnings(poolId, riskFree)</code>

Withdraw earnings from Pool.

## contracts.onClosePool

> Sample Code

```javascript
library.contracts.onClosePool(poolId, riskFree).then(() => {
  fetchPools()
})
```

<code class="sample">library.contracts.onClosePool(poolId, riskFree)</code>

Close Pool.

## contracts.onChangePrice

> Sample Code

```javascript
const options = {
  riskFree,
  type,
  marketInfo: marketInfoParam || marketInfo,
}

library.contracts.onChangePrice(poolId, value, options).then(() => {
  fetchPools()
})
```

<code class="sample">library.contracts.onChangePrice(poolId, value, options)</code>

Change Price for splitted tokens.

## contracts.onIncreaseCapacity

> Sample Code

```javascript
const options = {
  riskFree,
  type,
  marketInfo: marketInfoParam || marketInfo,
}

library.contracts.onIncreaseCapacity(poolId, value, options).then(() => {
  fetchPools()
})
```

<code class="sample">library.contracts.onIncreaseCapacity(poolId, value, options)</code>

Increase splitted tokens capacity.

## contracts.onDecreaseCapacity

> Sample Code

```javascript
const options = {
  riskFree,
  type,
  marketInfo: marketInfoParam || marketInfo,
}

library.contracts.onDecreaseCapacity(poolId, value, options).then(() => {
  fetchPools()
})
```

<code class="sample">library.contracts.onDecreaseCapacity(poolId, value, options)</code>

Decrease splitted tokens capacity.

## contracts.onRetireToken

> Sample Code

```javascript
const options = {
  riskFree,
  type,
  marketInfo: marketInfoParam || marketInfo,
}

library.contracts.onRetireToken(poolId, options).then(() => {
  fetchPools()
})
```

<code class="sample">library.contracts.onRetireToken(poolId, options)</code>

Retire offered token.

## contracts.onContribute

> Sample Code

```javascript
library.contracts.onContribute(poolId, amount, { riskFree }).then(() => {
  if (riskFree) {
    library.contracts.getRiskFreePools()
  } else {
    library.contracts.getRiskyPools()
  }
  library.contracts.getBalances()
})
```

<code class="sample">library.contracts.onContribute(poolId, value, options)</code>

Withdraw contributed balance from Pool.

## contracts.onWithdrawContribute

> Sample Code

```javascript
library.contracts.onWithdrawContribute(poolId, amount, { riskFree }).then(() => {
  if (riskFree) {
    library.contracts.getRiskFreePools()
  } else {
    library.contracts.getRiskyPools()
  }
  library.contracts.getBalances()
})
```

<code class="sample">library.contracts.onWithdrawContribute(poolId, value, options)</code>

Withdraw contributed balance from Pool.

## contracts.onPurchase

> Sample Code

```javascript
const marketInfo = [expiry, underlying, strike]
library.contracts.onPurchase(id, type, marketInfo).then(() => {
  library.contracts.getRiskFreePools()
  library.contracts.getRiskyPools()
})
```

<code class="sample">library.contracts.onPurchase(poolId, type, param)</code>

Purchase splitted token to Pool.

## contracts.onAvailLoan

> Sample Code

```javascript
library.contracts
  .onAvailLoan({
    currency,
    expiry,
    underlying,
    strike,
    amount,
  })
  .then(() => {
    library.contracts.getPositions()
  })
```

<code class="sample">library.contracts.onAvailLoan(form)</code>

Borrow a loan(create a position).

## contracts.onRepay

> Sample Code

```javascript
library.contracts.onRepay(positionId, amount).then(() => {
  library.contracts.getPositions()
})
```

<code class="sample">library.contracts.onRepay(positionId, amount)</code>

Re-pay to loan.

## contracts.onWithdrawCollateral

> Sample Code

```javascript
library.contracts.onWithdrawCollateral(positionId).then(() => {
  library.contracts.getPositions()
})
```

<code class="sample">library.contracts.onWithdrawCollateral(positionId)</code>

Withdraw collateral to loan.

## contracts.onTransfer

> Sample Code

```javascript
const { token, amount, to, id, data } = form
library.contracts.onTransfer({ token, amount, to, id, data }).then(() => {
  library.contracts.getBalances()
})
```

<code class="sample">library.contracts.onTransfer(form)</code>

Transfer Tokens.

| Key    | Type              | Default | Description             |
| ------ | ----------------- | ------- | ----------------------- |
| token  | `string`          | -       | Token name.             |
| amount | `number`,`string` | -       | Amount to transfer.     |
| to     | `address`         | -       | Address to transfer.    |
| id     | `number`,`string` | -       | `id` of token for MFTs. |
| data   | `string`          | ''      | Data                    |

# Web3Utils Class

It uses origin Web3 [latest](https://web3js.readthedocs.io/en/1.2.4/). And you can find its definition on
[Lendroid Services](https://github.com/lendroidproject/lendroid-2-js/blob/master/src/services/web3Utils.ts)

## toWei

> Sample Code

```javascript
library.web3Utils.toWei(1)
// return : "1000000000000000000"
```

<code class="sample">library.web3Utils.toWei(number)</code>

Converts any `ether value` value into `wei`. It refers
[Web3](https://web3js.readthedocs.io/en/v1.2.4/web3-utils.html#towei)

### Parameters

| Key    | Type               | Default | Description     |
| ------ | ------------------ | ------- | --------------- |
| number | `string,number,BN` | -       | Value of ether. |

<mark class="notice">
`wei` are the smallest ethere unit, and you should always make calculations in wei and convert only for display reasons.
</mark>

## fromWei

> Sample Code

```javascript
library.web3Utils.fromWei('1000000000000000000')
// return : "1"
```

<code class="sample">library.web3Utils.fromWei(number)</code>

Converts any `wei` value into `ether value`. It refers
[Web3](https://web3js.readthedocs.io/en/v1.2.4/web3-utils.html#fromwei)

### Parameters

| Key    | Type               | Default | Description     |
| ------ | ------------------ | ------- | --------------- |
| number | `string,number,BN` | -       | Value of ether. |

## toBN

> Sample Code

```javascript
library.web3Utils.toBN(1234).toString()
// return : "1234"
```

<code class="sample">library.web3Utils.toBN(number)</code>

Convert any given value (including `BigNumber.js` instances) into a `BN.js` instance, for handling big numbers in
JavaScript. It refers [Web3](https://web3js.readthedocs.io/en/v1.2.4/web3-utils.html#tobn)

### Parameters

| Key    | Type               | Default | Description     |
| ------ | ------------------ | ------- | --------------- |
| number | `string,number,BN` | -       | Value of ether. |

## toDecimal

> Sample Code

```javascript
library.web3Utils.toDecimal(0xea).toString()
// return : 234
```

<code class="sample">library.web3Utils.toDecimal(hex)</code>

Number representation of a given HEX value. It refers
[Web3](https://web3js.readthedocs.io/en/v1.2.4/web3-utils.html#hextonumber)

### Parameters

| Key | Type         | Default | Description |
| --- | ------------ | ------- | ----------- |
| hex | `string|HEX` | -       | -           |

## substract

> Sample Code

```javascript
library.web3Utils.substract(1, 1)
// return : 0
```

BN Substraction between numbers. [Reference](https://mikemcl.github.io/bignumber.js/#minus)

## substractBN

> Sample Code

```javascript
library.web3Utils.substractBN('1000000000000000000', '1000000000000000000')
// return : 0
```

Substract between Big-Numbers. [Reference](https://mikemcl.github.io/bignumber.js/#minus)

## createContract

> Sample Code

```javascript
library.web3Utils.createContract(contractABI, supportTokens[token][network])
```

<code class="sample">createContract(abi, address)</code>

Returns Web3 contract for ABI and contract address.

### Parameters

| Key     | Type     | Default | Description        |
| ------- | -------- | ------- | ------------------ |
| abi     | `object` | -       | Contract ABI.      |
| address | `string` | -       | Contract Addresss. |

## sendSignedTransaction

> Sample Code

```javascript
library.web3Utils.sendSignedTransaction(approval._signed_transaction)
```

<code class="sample">sendSignedTransaction(signedTransactionData)</code>

Returns Web3 contract for ABI and contract address.

### Parameters

| Key                   | Type     | Default | Description            |
| --------------------- | -------- | ------- | ---------------------- |
| signedTransactionData | `string` | -       | Signed Data by `web3`. |

## getBlockTimeStamp

> Sample Code

```javascript
library.web3Utils.getBlockTimeStamp()
```

<code class="sample">getBlockTimeStamp()</code>

Return latest block `timestamp`.
