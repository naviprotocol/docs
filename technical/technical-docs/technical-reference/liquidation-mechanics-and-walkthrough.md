# Liquidation Mechanics & Walkthrough

The health of the NAVI Protocol is dependant on the 'health' of the loans within the system, also known as the 'health factor'. When the 'health factor' of an account's total loans is below 1, anyone can call the [`execute_liquidation`](broken-reference) to the `LendingPool` contract, paying back part of the debt owed and receiving discounted collateral in return.

This incentivises third parties to participate in the health of the overall protocol, by acting in their own interest (to receive the discounted collateral) and as a result, ensure loans are sufficiently collateralised.

There are multiple ways to participate in liquidations:

1. By calling the  [`execute_liquidation`](broken-reference) directly in the LendingPool contract.
2. By creating your own automated bot or system to liquidate loans.

For liquidation calls to be profitable, you must take into account the gas cost involved in liquidating the loan. If a high gas price is used, then the liquidation may be unprofitable for you. See the [Calculating profitability vs gas cost](liquidation-mechanics-and-walkthrough.md#calculating-profitability-vs-gas-cost) section for more details.

## Prerequisites

When making a [`execute_liquidation`](broken-reference) call,  you must:

* Know the account (i.e. the Sui address: `user`) whose health factor is below 1.
* Know the valid debt amount (`debt_to_cover`) and debt asset (`debt`) that can be paid.
  * The close factor is 0.5, which means that only a maximum of 50% of the debt can be liquidated per valid `execute_liquidation`.
  * You can set the `debt_to_cover` to `uint(-1)` and the protocol will proceed with the highest possible liquidation allowed by the close factor.
  * You must already have a sufficient balance of the debt asset, which will be used by the `execute_liquidation` to pay back the debts.
* Know the collateral asset (`collateral`) you are closing. I.e. the collateral asset that the user has 'backing' their outstanding loan that you will partly receive as a 'bonus'..

## 1. Getting accounts to liquidate

Only user accounts that have a health factor below 1 can be liquidated. There are multiple ways you can get the health factor, with most of them involving 'user account data'.

"Users" in the NAVI Protocol refer to a single Sui address that has interacted with the protocol. This can be an externally owned account or contract.

### On-chain

1. To gather user account data from on-chain data, one way would be to monitor emitted events from the protocol and keep an up to date index of user data locally.
   1. Events are emitted each time a user interacts with the protocol (deposit, repay, borrow, etc). See the contract source code for relevant events.
2. When you have the user's address, you can simply call [`get_user_account_data`](broken-reference) to read the user's current `health_factor`. If the `health_factor` is below 1, then the account can be liquidated.



## 2. Executing the liquidation call

Once you have the account(s) to liquidate, you will need to calculate the amount of collateral that _can_ be liquidated:

1. Use [`get_user_reserve_data`](broken-reference) on the [Lending Pool Lens](broken-reference) contract with the relevant parameters.
2.  Max debt that can be cleared by single liquidation call is given by the current close factor (which is 0.5 currently).\
    \
    $$debtToCover = (userStableDebt + userVariableDebt) * LiquidationCloseFactorPercent$$


3. For reserves that have `usage_as_collateral_enabled` as `true`, the `current_atoken_balance` and the current [liquidation bonus](https://naviprotocol.gitbook.io/navi-protocol-docs/parameters/lending-and-borrowing) gives the max amount of collateral that can be liquidated \
   \
   $$maxAmountOfCollateralToLiquidate = (debtAssetPrice * currentATokenBalance * liquidationBonus)/ collateralPrice$$

### Move

TODO

## 3. Setting up a bot

Depending on your environment, preferred programming tools and languages, your bot should:

* Ensure it has enough (or access to enough) funds when liquidating.
* Calculate the profitability of liquidating loans vs gas costs, taking into account the most lucrative collateral to liquidate.
* Ensure it has access to the latest protocol user data.
* Have the usual fail safes and security you'd expect for any production service.

### Calculating profitability vs gas cost

One way to calculate the profitability is the following:

1. Store and retrieve each collateral's relevant details such as address, decimals used, and [liquidation bonus as listed here](https://naviprotocol.gitbook.io/navi-protocol-docs/parameters/lending-and-borrowing).
2. Get the user's collateral balance (n`TokenBalance`).
3. Get the asset's price according to the [Aave's oracle contract](notion://www.notion.so/developers/the-core-protocol/price-oracle) (`getAssetPrice()`).
4. The maximum collateral bonus you can receive will be the collateral balance (2) multiplied by the liquidation bonus (1) multiplied by the collateral asset's price in ETH (3). Note that for assets such as USDC, the number of decimals are different from other assets.
5. The maximum cost of your transaction will be your gas price multiplied by the amount of gas used. You should be able to get a good estimation of the gas amount used by calling `estimateGas` via your web3 provider.
6. Your approximate profit will be the value of the collateral bonus (4) minus the cost of your transaction (5).

### **Example Liquidation Scenario**

* A user deposits $100 worth of SUI (70% LTV), and borrows $50 worth of BTC.
  * Supply Balance: $100
  * Borrow Balance: $50
  * Health Factor = 100 \* 0,7 / 50 = 1.4
* BTC price goes up by 50%
  * Supply Balance: $100
  * Borrow Balance: $75
  * Health Factor = 100 \* 0,7 / 75 = 0,9333

**User is now liquidate-able**

Liquidate positions with a **health factor** below 1.

When the health factor of a position is below 1, liquidators repay part or all of the outstanding borrowed amount on behalf of the borrower, while **receiving a discounted amount of collateral** in return (also known as a liquidation "bonus"). Liquidators can decide if they want to receive an equivalent amount of collateral aTokens, or the underlying asset directly. When the liquidation is completed successfully, the health factor of the position is increased, bringing the health factor above 1.

Liquidators can only close a certain amount of collateral defined by a close factor. Currently the **close factor is 0.5**. In other words, liquidators can only liquidate a maximum of 50% of the amount pending to be repaid in a position. The liquidation discount applies to this amount.

{% hint style="info" %}
_In most scenarios_, profitable liquidators will choose to liquidate as much as they can (50% of the `user` position).

To check a user's health factor, use [`get_user_account_data`](liquidation-mechanics-and-walkthrough.md#function-get\_user\_account\_data)
{% endhint %}
