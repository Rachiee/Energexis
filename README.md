# **Energexis Smart Contract**  
*Decentralized Renewable Energy Trading Platform*

## Overview  
**Energexis** is a decentralized platform for peer-to-peer (P2P) trading of renewable energy. It enables producers and consumers to trade energy securely and transparently using smart contracts, integrated with smart meters to track energy production and consumption in real-time.

### Key Features
- **Smart Meter Integration:** Registers and verifies smart meters for accurate energy production and consumption data.
- **P2P Energy Trading:** Facilitates the creation and execution of buy/sell orders for renewable energy between users.
- **Energy Balances:** Tracks available and locked energy units for trading purposes.
- **Platform Fees:** Collects a small fee on each trade to support platform maintenance.
- **Trading History:** Maintains a detailed record of completed transactions.

---

## Smart Contract Structure

### 1. **Constants**
Defines error codes and platform parameters:
- `ERR-NOT-AUTHORIZED`: Unauthorized access (Error Code `u1`).
- `ERR-INVALID-METER`: Invalid or non-existent smart meter (Error Code `u2`).
- `ERR-INSUFFICIENT-ENERGY`: Insufficient energy for the transaction (Error Code `u3`).
- `ERR-INVALID-PRICE`: Invalid price input (Error Code `u4`).
- `ERR-INSUFFICIENT-BALANCE`: Insufficient STX balance for transaction (Error Code `u5`).
- `ERR-ORDER-NOT-FOUND`: Order not found (Error Code `u6`).
- `ERR-ALREADY-REGISTERED`: Smart meter already registered (Error Code `u7`).

---

### 2. **Data Variables**
- **`admin`:** Stores the platform administrator's address.
- **`order-nonce`:** Tracks the unique identifier for each order.
- **`min-energy-unit`:** Minimum energy unit (in kWh) allowed for trading.
- **`platform-fee`:** Platform fee in basis points (default is 1%).

---

### 3. **Data Maps**
- **`smart-meters`:** Stores smart meter details (owner, meter ID, location, capacity, and verification status).
- **`energy-orders`:** Stores details of energy sell orders (seller, energy amount, price, expiry, and active status).
- **`energy-balance`:** Tracks available and locked energy balances for each user.
- **`trading-history`:** Records transaction details for historical reference.

---

## Key Functions

### 1. **Authorization**
```clarity
(define-private (is-admin) (is-eq tx-sender (var-get admin)))
```
Checks if the caller is the platform administrator.

---

### 2. **Smart Meter Registration**
```clarity
(define-public (register-smart-meter (meter-id (string-utf8 32)) (location (string-utf8 64)) (capacity uint)))
```
Registers a smart meter for energy tracking.  
- **Inputs:** `meter-id`, `location`, `capacity`  
- **Output:** Confirmation of registration.

---

### 3. **Smart Meter Verification**
```clarity
(define-public (verify-smart-meter (meter-owner principal)))
```
Verifies the authenticity of a smart meter.  
- **Only the admin** can execute this function.

---

### 4. **Record Energy Production**
```clarity
(define-public (record-energy-production (amount uint)))
```
Records energy production from a verified smart meter and updates the user’s energy balance.  
- **Input:** `amount` (in kWh)  
- **Output:** Updated energy balance.

---

### 5. **Create Sell Order**
```clarity
(define-public (create-sell-order (energy-amount uint) (price-per-unit uint) (blocks-until-expiry uint)))
```
Creates a sell order for available energy.  
- **Inputs:**  
  - `energy-amount`: Energy to sell (in kWh).  
  - `price-per-unit`: Price per kWh (in microSTX).  
  - `blocks-until-expiry`: Expiration time in blocks.

---

### 6. **Execute Buy Order**
```clarity
(define-public (execute-buy-order (order-id uint)))
```
Executes a buy order by transferring energy and payment between buyer and seller.  
- **Input:** `order-id`  
- **Output:** Order fulfillment and balance updates.

---

### 7. **Cancel Sell Order**
```clarity
(define-public (cancel-sell-order (order-id uint)))
```
Cancels an active sell order and unlocks the associated energy.  
- **Input:** `order-id`  
- **Output:** Order cancellation confirmation.

---

## Read-Only Functions
- **`get-meter-details`:** Retrieves smart meter details for a user.
- **`get-order-details`:** Retrieves details of a specific sell order.
- **`get-energy-balance`:** Retrieves the energy balance of a user.
- **`get-trade-history`:** Retrieves the trade history for a specific order and buyer.

---

## Usage Example

### Register a Smart Meter
```clarity
(contract-call? .Energexis register-smart-meter "METER123" "Lagos, Nigeria" u500)
```

### Verify a Smart Meter (Admin Only)
```clarity
(contract-call? .Energexis verify-smart-meter 'SP1234...')
```

### Record Energy Production
```clarity
(contract-call? .Energexis record-energy-production u100)
```

### Create a Sell Order
```clarity
(contract-call? .Energexis create-sell-order u50 u1000 u144)
```

### Execute a Buy Order
```clarity
(contract-call? .Energexis execute-buy-order u1)
```

### Cancel a Sell Order
```clarity
(contract-call? .Energexis cancel-sell-order u1)
```

---

## Security Considerations
- **Smart Meter Verification:** Only verified smart meters can record energy production.
- **Order Expiry:** Sell orders automatically expire after the specified number of blocks.
- **Admin Role:** Critical functions like verification are restricted to the admin.

---

## License
This smart contract is licensed under the MIT License.