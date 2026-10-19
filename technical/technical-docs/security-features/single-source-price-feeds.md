# Single-Source Price Feeds

Most assets on NAVI are priced from two independent providers: Pyth as the primary source and Supra as the secondary. A price that diverges between the two beyond the feed's threshold is not accepted. For some assets there is no second provider on Sui reliable enough to cross-check Pyth, so they are priced from Pyth alone. This page lists those assets and the controls that apply to them.

## Controls on single-source feeds

Every price update on a single-source feed must pass three checks before it is stored:

* **Staleness.** A Pyth price older than the feed's maximum age is rejected.
* **Span check.** A price that moves further from the last accepted price than the feed's maximum span is rejected, and the stored price is left unchanged.
* **Price band.** A price outside the feed's minimum and maximum effective price is rejected.

Their risk parameters are set conservatively:

* In the main market, the LTV of a single-source asset is in line with or below that of the comparable multi-source asset: USD-pegged assets at 75% or less against USDC at 80%, BTC wrappers at 67% or less against BTC at 65%, and other assets at 60% or less.
* Higher LTVs exist only in isolated markets, each with its own supply cap, so a mispricing cannot spread to the main market.
* Every reserve has a supply cap, which bounds the exposure to the asset in that market.

We review these parameters as the reliability of each feed changes, and add a second provider as soon as one proves reliable on Sui.

## Current configuration

Read from Sui mainnet at checkpoint 335892458 (2026-10-19). The live values are on chain: the feeds in the oracle's `OracleConfig` (`0x1afe1cb83634f581606cc73c4487ddd8cc39a944b951283af23f7d69d5589478`), and the risk parameters in each market's `Storage`.

NS and WAL name Pyth as both primary and secondary, which the oracle treats as having no secondary; they are listed here with the other single-source feeds.

### Price feeds

| Asset | Oracle ID | Secondary | Max span |
| --- | --- | --- | --- |
| AUSD | 9 | None | 7% |
| NS | 13 | Pyth (same as primary) | 30% |
| BLUE | 17 | None | 30% |
| SOL | 22 | None | 10% |
| LBTC | 23 | None | 7% |
| WAL | 24 | Pyth (same as primary) | 30% |
| HAEDAL | 25 | None | 30% |
| xBTC | 26 | None | 7% |
| IKA | 27 | None | 40% |
| enzoBTC | 28 | None | 7% |
| MBTC | 29 | None | 7% |
| YBTC.B | 30 | None | 7% |
| XAUM | 31 | None | 30% |
| WBTC | 32 | None | 7% |
| suiUSDe | 33 | None | 7% |
| USDSUI | 34 | None | 7% |
| eACRED | 35 | None | 7% |
| XAGM | 36 | None | 7% |
| HIGH | 37 | None | 7% |

### Risk parameters

An LTV of 0% means the asset cannot be used as collateral in that market.

| Asset | Market | LTV | Liquidation threshold | Supply cap (tokens) | Borrowable |
| --- | --- | --- | --- | --- | --- |
| AUSD | Main | 0% | 85% | 10 | No |
| NS | Main | 0% | 35% | 10 | No |
| NS | Isolated 3 | 50% | 55% | 46,000,000 | No |
| BLUE | Main | 0% | 30% | 10 | No |
| BLUE | Isolated 3 | 50% | 55% | 4,000,000 | No |
| SOL | Main | 60% | 65% | 1,000 | Yes |
| LBTC | Main | 65% | 70% | 70 | Yes |
| WAL | Main | 60% | 65% | 160,000,000 | Yes |
| HAEDAL | Main | 0% | 40% | 10 | No |
| HAEDAL | Isolated 3 | 50% | 55% | 12,000,000 | No |
| xBTC | Main | 67% | 70% | 205 | Yes |
| xBTC | Isolated 6 | 80% | 82% | 80 | No |
| IKA | Main | 0% | 20% | 10 | No |
| IKA | Isolated 3 | 0% | 0% | 1,000,000 | No |
| enzoBTC | Main | 62% | 77% | 435 | No |
| MBTC | Main | 65% | 70% | 200 | No |
| YBTC.B | Main | 0% | 45% | 0.5 | No |
| XAUM | Main | 0% | 45% | 10 | No |
| XAUM | Isolated 2 | 55% | 60% | 1,000 | No |
| WBTC | Main | 65% | 70% | 100 | Yes |
| WBTC | Isolated 5 | 80% | 82% | 80 | No |
| suiUSDe | Main | 75% | 80% | 5,000,000 | Yes |
| suiUSDe | Isolated 1 | 0% | 0% | 4,000,000 | Yes |
| USDSUI | Main | 75% | 78% | 15,000,000 | Yes |
| eACRED | Isolated 1 | 0% | 45% | 5,000 | No |
| XAGM | Isolated 2 | 50% | 55% | 25,000 | No |
| HIGH | Isolated 10 | 70% | 75% | 100 | No |
