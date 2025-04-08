# 💸 FundMe – A Simple Crowdsourcing Smart Contract

FundMe is a basic Ethereum-based crowdfunding contract written in Solidity. It allows users to send ETH to support a project or cause, while ensuring all contributions meet a minimum USD value using live Chainlink price feeds. Only the contract owner can withdraw the collected funds.

---

## 🔍 Overview

- Accept ETH from users (crowdfunding).
- Enforce a minimum contribution in **USD** (default: $5).
- Use Chainlink to convert ETH to USD.
- Allow **only the contract owner** to withdraw funds.
- Support fallback and receive functions to auto-trigger funding on ETH transfer.

---

## 📦 Features

- ✅ Minimum USD value enforcement via Chainlink
- ✅ `fund()` function with ETH to USD conversion
- ✅ Owner-only `withdraw()` and `cheaperWithdraw()` functions
- ✅ `receive()` and `fallback()` for handling ETH transfers
- ✅ Gas optimization using `cheaperWithdraw()`
- ✅ Custom error (`FundMe__NotOwner`) for cleaner revert handling

---

## 🧰 Dependencies

- [Chainlink Contracts](https://github.com/smartcontractkit/chainlink)
- `AggregatorV3Interface` for real-time ETH/USD pricing
- `PriceConverter` library (you'll need to create or import this utility)

---

## 🛠 Constructor

```solidity
constructor(address priceFeed)
```

The constructor takes the address of a Chainlink price feed, allowing the contract to get real-time ETH/USD data.

Example price feed (Sepolia Testnet):

```solidity
ETH/USD = 0x694AA1769357215DE4FAC081bf1f309aDC325306
```

---

## 💸 Funding

### `fund()`

Any user can send ETH using this function. It checks that the amount (in USD) is at least `$5`.

```solidity
require(
    msg.value.getConversionRate(s_priceFeed) >= MINIMUM_USD,
    "You need to spend more ETH!"
);
```

---

## 🔓 Withdrawals

### `withdraw()`

Allows the contract owner to withdraw all collected funds.

### `cheaperWithdraw()`

Gas-optimized version for large contributor lists.

Both functions reset contributor balances and send funds using the `.call()` method.

---

## 🧾 Access Control

Only the deployer (contract owner) can withdraw funds:

```solidity
modifier onlyOwner() {
    if (msg.sender != i_owner) revert FundMe__NotOwner();
    _;
}
```

---

## 🪙 ETH Handling

The contract gracefully accepts ETH through both `receive()` and `fallback()` functions. If ETH is sent directly to the contract without any function call, `fund()` will still be executed.

```solidity
fallback() external payable {
    fund();
}

receive() external payable {
    fund();
}
```

---

## 🔎 Getter Functions

- `getAddressToAmountFunded(address)` — Returns total amount funded by an address.
- `getFunder(uint256)` — Returns address of a funder by index.
- `getOwner()` — Returns contract owner.
- `getVersion()` — Returns Chainlink price feed version.

---

## 📘 To-Do / Future Improvements

- Add **events** for funding and withdrawals
- Create a **frontend** for interacting with the contract
- Add **refund** mechanism
- Implement **goal tracking** or funding thresholds
- Use **OpenZeppelin's Ownable** for cleaner access control

---

## 📝 License

MIT License

---

## 🙌 Acknowledgements

Inspired by [Patrick Collins' Solidity Bootcamp](https://github.com/smartcontractkit/full-blockchain-solidity-course-py)

---
