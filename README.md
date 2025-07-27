 Hi, I’m iamparth.eth 


# PropToken - Real Estate Tokenization System

## Architecture Overview

### Three Smart Contracts Working Together

**PropertyDeed.sol** - Master Property NFT
* Each NFT = One unique real estate property
* Only contract owner can mint new deeds
* Stores property metadata via URI

**PropertyFractions.sol** - Fractional Ownership Tokens
* ERC-20 tokens representing shares of one property
* Each property gets its own separate ERC-20 contract
* Fixed supply per property (e.g., 1,000,000 tokens)

**TokenizationManager.sol** - Main Controller
* Orchestrates the entire tokenization process
* Holds PropertyDeed NFTs in escrow during fractionalization
* Handles sales and distribution of fractions

### Contract Interaction Flow

```
                    Property Owner
                         |
                         | 1. tokenizeProperty()
                         ▼
              ┌─────────────────────────┐
              │   TokenizationManager   │◄─── 5. startDistribution()
              │    (Main Orchestrator)  │
              └─────────────────────────┘
                    |              |
         2. mint NFT|              |3. deploy new contract
                    ▼              ▼
         ┌─────────────────┐  ┌─────────────────┐
         │   PropertyDeed  │  │PropertyFractions│
         │   (ERC-721 NFT) │  │  (ERC-20 Token) │
         └─────────────────┘  └─────────────────┘
                    ▲              ▲
         4. lock NFT|              |7. transfer fractions
                    |              |
              ┌─────────────────────────┐
              │   TokenizationManager   │◄─── 6. buyFractions() (Investors)
              │                         │
              └─────────────────────────┘
```

### Why This Architecture?

* **Separation of Concerns**: Each contract has one clear responsibility
* **Security**: PropertyDeed NFTs locked in escrow prevent manipulation
* **Scalability**: Each property gets its own ERC-20 contract with custom parameters
* **Standard Compliance**: Uses battle-tested OpenZeppelin implementations

## Compilation

```bash
npx hardhat compile
```

## Testing

```bash
npx hardhat test
```

**Test Coverage:**
* Property tokenization process
* Distribution setup and access control
* Fraction buying with payment validation
* Security and edge cases
* ERC-721/ERC-20 compliance

## Deployment

### 1. Start Local Network
```bash
npx hardhat node
```

### 2. Deploy Contracts
```bash
npx hardhat run scripts/deploy.js --network localhost
```

### 3. Deployment Process
The deploy script will:
1. Deploy PropertyDeed contract
2. Deploy TokenizationManager contract
3. Output contract addresses

### 4. Post-Deployment Setup
Transfer PropertyDeed ownership to TokenizationManager:
```javascript
await propertyDeed.transferOwnership(tokenizationManager.address);
```

## Usage

### Tokenize a Property
```javascript
await manager.tokenizeProperty(
  "Villa Token",           // name
  "VILLA",                // symbol  
  1000000,                // total supply
  "https://metadata.uri"  // property URI
);
```

### Start Sales
```javascript
const pricePerFraction = ethers.parseEther("1.0"); // 1 ETH per fraction
await manager.startDistribution(propertyId, pricePerFraction);
```

### Buy Fractions
```javascript
const amount = 100;
const cost = ethers.parseEther("100"); // 100 ETH
await manager.buyFractions(propertyId, amount, { value: cost });
```

### Check Balance
```javascript
const balance = await manager.getUserFractionBalance(propertyId, userAddress);
```

## Key Functions

| Function | Purpose | Access |
|----------|---------|---------|
| `tokenizeProperty()` | Create tokenized property | Anyone |
| `startDistribution()` | Enable fraction sales | Property Owner |
| `buyFractions()` | Purchase fractions | Anyone (with ETH) |
| `getUserFractionBalance()` | Check user balance | View |

## Security Features

* **Access Control**: Only property owners can start distribution
* **Payment Validation**: Exact ETH amount required for purchases
* **NFT Escrow**: PropertyDeeds locked during fractionalization
* **OpenZeppelin**: Battle-tested contract implementations
* **Input Validation**: All user inputs validated

## Project Structure

```
├── contracts/
│   ├── PropertyDeed.sol
│   ├── PropertyFractions.sol  
│   └── TokenizationManager.sol
├── test/
│   └── tokenization-test.js
├── scripts/
│   └── deploy.js
└── hardhat.config.js
```
