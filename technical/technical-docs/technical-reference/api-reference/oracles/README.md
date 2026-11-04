# Oracles

Here we'll take a look at a core piece of infrastructure of the protocol, the Oracles.

Package Name: `oracle`&#x20;

Source Code: [oracle on GitHub](https://github.com/naviprotocol/navi-smart-contracts/tree/main/oracle)

* [`oracle.move`](broken-reference)

## FAQ

<details>

<summary>What are oracles used for?</summary>

Oracles are an on-chain price feed that is used to get the price of any asset into a relative unit. This is important for calculating collateral ratios to determine how much a user can borrow against their collateral, as well as proving that a position is liquidate-able.

</details>

<details>

<summary>What types of oracles can be used?</summary>

An oracle is just an on-chain representation of data, so this can be abstracted into a generic interface. Wrappers will be written for different oracle types including:

* Generic “mock” oracles that return a constant value for testing.
* [Supra](https://data.supraoracles.com/networks) oracles.
* AMM-based TWAP oracles.

</details>

## Smart Contracts

### `NaviOracle`

The NaviOracle is a registry that maps Asset → Oracle Implementation. This is used so that when any of the liquidity protocol code needs to query the price of an asset, it can simply call this contract to get an address of an oracle, then call the given address with a standard interface which implements the oracle.

#### Read Methods

* `price`
  * Returns the price of an asset, in native asset (i.e. SUI).
  * Params
    * `asset_id`: Address of an asset to get a price for.

#### Write Methods

* `set_price_oracle`
  * ONLY allowed to be called by owner (eventually will be DAO).
  * Sets a price oracle for a specific asset.
  * Set to null (0?) to remove an oracle.

### `MockPriceOracle` - ONLY for testing

#### Read Methods

* `price`
  * Returns a hardcoded price for testing.
  * Params
    * `asset_id`: Address of an asset to get a price for.

### `SupraPriceOracle`

#### Read Methods

* `price`
  * Calls Supra oracle for particular asset and returns a price in native asset (i.e. SUI).
  * Params
    * `asset_id`: Address of an asset to get a price for.
  * Implementation
    * Query Supra oracle and get price.
    * (Possibly?) Keep an internal registry of Supra oracles for a given asset.

## Code Sample

`NaviOracle`

```solidity
module oracle::navi_oracle {

    // Part 1: Imports
    use sui::object::{Self, UID};
    use sui::transfer;
    use sui::tx_context::{Self, TxContext};
    use std::vector as vec;

    struct OracleAdmin has key, store { id: UID }

    struct Oracle has store {
        asset: address,
        oracle: address,
    }

    struct Oracles has store {
        oracles: vector<Oracle>
    }

    struct Forge has key, store {
        id: UID,
        swords_created: u64,
    }

    fun init(ctx: &mut TxContext) {
        transfer::public_transfer(OracleAdmin { id: object::new(ctx) }, tx_context::sender(ctx));
    }

    // ======= Admin Functions =======
    public entry fun add_oracle(
        _: &OracleAdmin,
        reg: &mut Oracles,
        asset: address,
        oracle: address,
        _ctx: &mut TxContext
    ) {
        vec::push_back(&mut reg.oracles, Oracle { asset, oracle });
    }

    public entry fun get_price(reg: &mut Oracles, asset: address): u64 {
        let i = 0;
        let price;
        while (i < vec::length(&reg.oracles)) {
            let oracle = vec::borrow(&reg.oracles, i);
            if (oracle.asset == asset) {
                price = oracle.oracle.price();
            };
            i = i + 1;
        };
        price
    }
}
```
