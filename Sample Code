// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract NotificationBlockchain {
    // Structure to represent a transaction
    struct Transaction {
        address sender;
        address receiver;
        uint256 amount;
        uint256 timestamp;
        bytes signature;
    }

    // Events for notifications
    event TransactionNotification(
        address indexed sender,
        address indexed receiver,
        uint256 amount,
        uint256 timestamp
    );

    // Array to store pending transactions
    Transaction[] public pendingTransactions;

    // Add a new transaction and emit a notification
    function addTransaction(
        address receiver,
        uint256 amount,
        bytes memory signature
    ) public {
        Transaction memory newTransaction = Transaction({
            sender: msg.sender,
            receiver: receiver,
            amount: amount,
            timestamp: block.timestamp,
            signature: signature
        });

        require(verifySignature(newTransaction), "Invalid transaction signature");
        pendingTransactions.push(newTransaction);

        // Emit a notification event
        emit TransactionNotification(msg.sender, receiver, amount, block.timestamp);
    }

    // Verify the signature of a transaction
    function verifySignature(Transaction memory transaction) private pure returns (bool) {
        bytes32 txHash = keccak256(
            abi.encodePacked(transaction.sender, transaction.receiver, transaction.amount, transaction.timestamp)
        );
        return recoverSigner(txHash, transaction.signature) == transaction.sender;
    }

    // Recover the signer from the hash and signature
    function recoverSigner(bytes32 hash, bytes memory signature) private pure returns (address) {
        bytes32 r;
        bytes32 s;
        uint8 v;

        // Extract signature parameters
        assembly {
            r := mload(add(signature, 32))
            s := mload(add(signature, 64))
            v := byte(0, mload(add(signature, 96)))
        }

        return ecrecover(hash, v, r, s);
    }

    // Retrieve all pending transactions
    function getPendingTransactions() public view returns (Transaction[] memory) {
        return pendingTransactions;
    }

    // Retrieve pending transactions for a specific receiver
    function getPendingTransactionsForReceiver(address receiver) public view returns (Transaction[] memory) {
        uint256 count = 0;
        for (uint256 i = 0; i < pendingTransactions.length; i++) {
            if (pendingTransactions[i].receiver == receiver) {
                count++;
            }
        }

        Transaction[] memory receiverTransactions = new Transaction[](count);
        uint256 index = 0;
        for (uint256 i = 0; i < pendingTransactions.length; i++) {
            if (pendingTransactions[i].receiver == receiver) {
                receiverTransactions[index] = pendingTransactions[i];
                index++;
            }
        }
        return receiverTransactions;
    }
}
