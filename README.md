 Hi, I’m iamparth.eth 


## 🔄 Contract Interaction Flow

```mermaid
graph TD
    A[Property Owner] -->|1. tokenizeProperty()| B[TokenizationManager]
    B -->|2. mint NFT| C[PropertyDeed]
    B -->|3. deploy new contract| D[PropertyFractions]
    B -->|4. lock NFT in manager| B
    A -->|5. startDistribution()| B
    E[Investors] -->|6. buyFractions()| B
    B -->|7. transfer fractions| D
```
