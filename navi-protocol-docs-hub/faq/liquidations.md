# Liquidations

## Introduction

A liquidation is a process that occurs when a borrower's health factor goes below 1 due to their collateral value not properly covering their loan/debt value. This might happen when the collateral decreases in value or the borrowed debt increases in value against each other. This collateral vs loan value ratio is shown in the [health factor](https://naviprotocol.gitbook.io/navi-protocol-docs/protocol-mechanics/liquidations).\
In a liquidation, up to 50% of a borrower's debt is repaid and that value + liquidation fee is taken from the collateral available, so after a liquidation that amount liquidated from your debt is repaid.

## How much is the liquidation penalty?

The liquidation penalty (or bonus for liquidators) depends on the asset used as collateral. You can find every assets' liquidation fee in the [risk parameters section](https://naviprotocol.gitbook.io/navi-protocol-docs/parameters/lending-and-borrowing).

## Can you give me an example?

Sure! A couple of them here:

**Example 1**&#x20;\
Bob deposits 10 SUI and borrows 5 SUI worth of USDC.&#x20;\
If Bob’s Health Factor drops below 1 his loan will be eligible for liquidation.&#x20;\
A liquidator can repay up to 50% of a single borrowed amount = 2.5 SUI worth of USDC.&#x20;\
In return, the liquidator can claim a single collateral which is SUI (5% bonus). &#x20;\
The liquidator claims 2.5 + 0.125 SUI for repaying 2.5 SUI worth of USDC.

**Example 2**&#x20;\
Bob deposits 5 SUI and 4 SUI worth of WBTC, and borrows 5 SUI worth of USDC&#x20;\
If Bob’s Health Factor drops below 1 his loan will be eligible for liquidation.&#x20;\
A liquidator can repay up to 50% of a single borrowed amount = 2.5 SUI worth of USDC.&#x20;\
In return, the liquidator can claim a single collateral, as the liquidation bonus is higher for WBTC (15%) than SUI (5%) the liquidator chooses to claim WBTC. &#x20;\
The liquidator claims 2.5 + 0.375 SUI worth of YFI for repaying 2.5 SUI worth of USDC.

## How can I avoid getting liquidated?

To avoid liquidation you can raise your health factor by depositing more collateral assets or repaying part of your loan. By default, repayments increase your health factor more than deposits. Also, it's important to monitor your health factor and keep it high to avoid a liquidation. Keeping your health factor over 2, for example, gives you more of a margin to avoid a liquidation.  \
\
You should be mindful of the stablecoin price fluctuations due to market conditions and how it might affect your healthfactor. For example, the market price of USDC 1.00 might not equal exactly USD 1.00, but for example USD 0.95. The price fluctuations of stablecoins, like any assets, affects your healthfactor.&#x20;

## Can I participate in the liquidations ecosystem?

Yes, liquidations are open to anyone, but there is a lot of competition. Normally liquidators develop their own solutions and bots to be the first ones liquidating loans to get the liquidation bonus.&#x20;
