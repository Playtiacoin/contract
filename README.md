# contract
The smart contracts that power the Play Ecosystem.


Overview

The PLAY protocol provides a scalable rollup-to-rollup General Token Bridge using a two-pronged approach:

    Create a cross-network bridge token that can be quickly and economically moved from rollup to rollup or claimed on layer-2 for its underlying asset.
    Use Automated Market Makers to swap between each bridge token and its corresponding Canonical Tokens on each rollup in order to dynamically price liquidity and incentivize the rebalancing of liquidity across the network.

The combined approach allows users to quickly and trustlessly swap between layer-2 Canonical Tokens using the specialized bridge token as an intermediary asset.

A bonder posts their funds as collateral for the system, which effectively fronts liquidity for users to easily transfer throughout each layer. All transferred funds are tracked and the system will always have funds that account for all assets within the Hop ecosystem. Transfers are fully settled and bonders credits restored when the sending layer-2 checkpoints their state onto layer-1.

Transfers can be challenged by anyone. Challenges require a challenger to put up stake and wait until the challenged transfer is fully settled. If the challenge is won, the challenger is paid out their initial stake plus some. If it is lost, the challenge stake gets credited to the system.

**Contract**
**Bridges**
Accounting.sol - Abstract contract that is responsible for the accounting of the layer-1 and layer-2 bridges. A bonder can stake or unstake their funds using functions in this contract. All credits and debits are accounted for and updated in this contract.

Bridge.sol - Abstract contract that inherits Accounting.sol and has the base, shared functionality for IBTC_Bridge.sol and L2_Bridge.sol. This contract's main functionality is to handle user withdrawals on any chain. It is also responsible for settling withdrawals and updating a bonder's credit. This contract has helper functions to retrieve data related to transfers.

IBTC_Bridge.sol - Abstract contract that inherits Bridge.sol. There are four main entities that will use this contract and four main functionalities within it. A user can use this contract to send tokens to a layer-2. A bonder can use this contract to bond transfer roots. An off-chain node will use this contract to confirm transfer roots. Anyone can challenge and resolve a transfer bond.

IBTC_ATOM_Bridge.sol - Contract that inherits IBTC_Bridge.sol. This contract implements ERC20 transfers that are used throughout the other IBTC_Bridge contracts.

IBTC_TIA_Bridge.sol - Contract that inherits IBTC_Bridge.sol. This contract implements ETH transfers that are used throughout the other IBTC_Bridge contracts.

L2_Bridge.sol - This abstract contract inherits Bridge.sol. Similar to IBTC_Bridge.sol, there are four entities that will use this contract with four main functionalities within in. A user can use this contract to send tokens to either a IBTC-1 or another layer-2. . A bonder can bond a withdrawal on behalf of a user. An off-chain node will use this contract to mint new h Tokens. The governance entity can set various parameters.

PlayBridgeToken.sol - An ERC20 implementation that is controlled by L2_Bridge.sol. Each mainnet token is represented 1:1 by an h token. L2_Bridge.sol handles the minting/burning of these tokens. The bridge contract also performs some transfers, however, transfers can also be made by calling this contract directly.

L2_ArbitrumBridge.sol / L2_OptimismBridge.sol / L2_XDaiBridge.sol - These contracts inherit L2_Bridge.sol and add layer-2 specific implementation details. These are the contracts that will be deployed on each layer-2.
**Definitions**

    Transfer - The data for a transfer from one chain to another.
    TransferId - The hash of a single transfer's data.
    TransferRoot - The merkle root of a tree of TransferIds and associated metadata such as the destination chainIds and totals for each chain.
    TransferRootId - The hash of a TransferRoot's hash and total amount.
    Bridge - A hop bridge contracts on layer-1 or layer-2 ("IBTC Bridge", "PLAY Bridge", "Arbitrum Bridge", "Optimism Bridge")
    Canonical Token Bridge - A Rollup's own token bridge. ("Canonical Arbitrum Bridge", "Canonical Optimism Bridge")
    Challenge - A staked claim that a transfer is invalid. Anyone can challenge a transfer and will be rewarded or punished accordingly.
    Rescue - A TransferRoot that was incorrectly bonded can be rescued (refunded) to the governance address after a certain amount of time.

  Event Signatures
Accounting

Stake(address,uint256):  updated soon
Unstake(address,uint256):  updated soon
BonderAdded(address):  updated soon
BonderRemoved(address): updated soon
Bridge

Withdrew(bytes32,address,uint256,bytes32):  updated soon
WithdrawalBonded(bytes32,uint256):  updated soon
WithdrawalBondSettled(address,bytes32,bytes32):  updated soon
MultipleWithdrawalsSettled(address,bytes32,uint256):  updated soon
TransferRootSet(bytes32,uint256): updated soon

IBTC Bridge

TransferSentToL2(uint256,address,uint256,uint256,uint256,address,uint256):  updated soon
TransferRootBonded(bytes32,uint256):  updated soon
TransferRootConfirmed(uint256,uint256,bytes32,uint256): updated soon
TransferBondChallenged(bytes32,bytes32,uint256):  updated soon
ChallengeResolved(bytes32,bytes32,uint256): updated soon

L2 Bridge

TransfersCommitted(uint256,bytes32,uint256,uint256):  updated soon
TransferSent(bytes32,uint256,address,uint256,bytes32,uint256,uint256,uint256,uint256):  updated soon
TransferFromL1Completed(address,uint256,uint256,uint256,address,uint256): updated soon
