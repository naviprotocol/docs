# Pool

The `pool` is the core contract through which users will interact with the protocol. It allows for all the funding operations.

Package Name: `pool`

Source Code: [pool on GitHub](https://github.com/naviprotocol/navi-smart-contracts/blob/main/lending_core/sources/pool.move)

* [`pool.move`](broken-reference)

## Contract API reference

### Write Methods

#### **`function deposit`**

Deposits `amount` of an `asset` into the protocol, minting the same `amount` of corresponding nTokens

#### **`function withdraw`**

Withdraws `amount` of the underlying `asset`, i.e. redeems the underlying token and burns the aTokens.



{% hint style="info" %}
💡 When withdrawing `to another` address, `msg.sender`should have`nToken` that will be burned by LendingPool .
{% endhint %}

#### **`function borrow`**

Borrows `amount` of `asset` with, sending the `amount` to `msg.sender`

#### **`function repay`**

Repays a debt `amount` of `asset`

#### **`function enable_as_collateral`**

Lets a user sets the `asset` of to be allowed to be used as collateral or not.

#### **`function execute_liquidation`**

What needs to be know:

* The collateral asset
* The debt asset
* User
* The amount of debt that needs to be covered by the liquidator

**Example Liquidation Scenario**

* A user deposits $100 worth of SUI (70% LTV), and borrows $50 worth of BTC.
  * Supply Balance: $100
  * Borrow Balance: $50
  * Health Factor = 100 \* 0,7 / 50 = 1.4
* BTC price goes up by 50%
  * Supply Balance: $100
  * Borrow Balance: $75
  * Health Factor = 100 \* 0,7 / 75 = 0,9333

**User is now liquidate-able**

Liquidate positions with a **health factor** below 1. Also see our [Liquidations guide](notion://www.notion.so/developers/guides/liquidations).

When the health factor of a position is below 1, liquidators repay part or all of the outstanding borrowed amount on behalf of the borrower, while **receiving a discounted amount of collateral** in return (also known as a liquidation "bonus"). Liquidators can decide if they want to receive an equivalent amount of collateral aTokens, or the underlying asset directly. When the liquidation is completed successfully, the health factor of the position is increased, bringing the health factor above 1.

Liquidators can only close a certain amount of collateral defined by a close factor. Currently the **close factor is 0.5**. In other words, liquidators can only liquidate a maximum of 50% of the amount pending to be repaid in a position. The liquidation discount applies to this amount.

{% hint style="info" %}
_In most scenarios_, profitable liquidators will choose to liquidate as much as they can (50% of the `user` position).

To check a user's health factor, use [`get_user_account_data`](./#function-get\_user\_account\_data)
{% endhint %}

#### **`function flash_loan`**

See the NAVI flash loan implementation [here](https://github.com/naviprotocol/navi-smart-contracts/blob/main/lending\_core/sources/flash\_loan.move).

### View Methods

#### **`function get_market_reserve_data`**

Returns the state of the reserve of a specific market

* Liquidity Index
* Current Supply / Liquidity in Ray
* Current Interest Rate
* Last Update Timestamp
* nToken address
* Underlying address
* Interest Rate Strategy Address

#### **`function get_user_account_data`**

Returns the user account data across all the reserves

* Total Collateral (in Sui)
* Total Debt (in Sui)
* LTV
* Health Factor
* Liquidation Threshold
* Borrowing power left

#### **`function get_market_configuration`**

Returns the configuration of the market

* LTV
* Liquidation Threshold
* Liquidation Bonus
* Reserve Factor
* Borrowing enabled
* Decimals of the market

#### **`function get_market_normalised_income`**

Returns the normalized income per unit of `asset` of a specific market

#### `function get_market_normalised_debt`

Returns the normalized variable debt per unit of `asset` of a specific market

#### **`function paused`**

Returns `true` if the LendingPool is paused.

#### **`function get_market_list`**

Returns the list of markets.

### Error Codes

TODO
