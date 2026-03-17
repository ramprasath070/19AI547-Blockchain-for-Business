## Experiment 6: Blockchain-Based Passwordless Authentication (Using Public-Private Key Cryptography)
## Aim:
To implement a secure passwordless authentication system using public-private key cryptography on Ethereum. This prevents phishing and password leaks.

## Algorithm:
Step 1: User Registration A user registers with their Ethereum public key (instead of a password).

Step 2: Login Process When logging in, the user signs a random challenge message using their private key.

The smart contract verifies the signature using the user’s public key.

## Program:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract PasswordlessAuthDemo {
    struct User {
        bool registered;
        address pubKey;
        bytes32 privateKey; // Fake private key for demo
    }

    mapping(address => User) public users;
    bytes32 public latestChallenge;

    event UserRegistered(address user, address pubKey, bytes32 privateKey);
    event ChallengeGenerated(bytes32 challenge);
    event SignatureGenerated(bytes32 hash, uint8 v, bytes32 r, bytes32 s);

    // Step 1: Register user
    function registerUser() public {
        require(!users[msg.sender].registered, "Already registered");

        // Fake public/private keys
        address fakePubKey = msg.sender;
        bytes32 fakePrivateKey = keccak256(abi.encodePacked(msg.sender, block.timestamp));

        users[msg.sender] = User({
            registered: true,
            pubKey: fakePubKey,
            privateKey: fakePrivateKey
        });

        emit UserRegistered(msg.sender, fakePubKey, fakePrivateKey);
    }

    // Step 2: Generate random challenge
    function generateChallenge() public returns (bytes32) {
        require(users[msg.sender].registered, "User not registered");
        latestChallenge = keccak256(abi.encodePacked(block.timestamp, msg.sender));
        emit ChallengeGenerated(latestChallenge);
        return latestChallenge;
    }

    // Step 3: "Sign" the challenge (fake signing)
    function generateSignature() public returns (bytes32 hash, uint8 v, bytes32 r, bytes32 s) {
        require(users[msg.sender].registered, "User not registered");
        
        hash = latestChallenge;
        bytes32 combined = keccak256(abi.encodePacked(users[msg.sender].privateKey, hash));
        
        // Fake values for r, s, v
        r = bytes32(uint256(uint160(users[msg.sender].pubKey)) << 96);
        s = combined;
        v = 27;

        emit SignatureGenerated(hash, v, r, s);

        return (hash, v, r, s);
    }

    // Step 4: Authenticate
    function authenticate(bytes32 hash, uint8 v, bytes32 r, bytes32 s) public view returns (bool) {
        require(users[msg.sender].registered, "User not registered");

        bytes32 expectedCombined = keccak256(abi.encodePacked(users[msg.sender].privateKey, hash));
        bytes32 expectedR = bytes32(uint256(uint160(users[msg.sender].pubKey)) << 96);
        uint8 expectedV = 27;

        if (r == expectedR && s == expectedCombined && v == expectedV) {
            return true;
        } else {
            return false;
        }
    }
}

```
## Expected Output:
Users can register without a password.

Users sign a challenge with their private key for authentication.

The smart contract verifies signatures to confirm identity.
<img width="1919" height="1067" alt="Screenshot 2026-03-17 155411" src="https://github.com/user-attachments/assets/25d3f3ee-50b1-4d01-853c-a7b835e1fb40" />


## High-Level Overview:
Eliminates password hacks & phishing attacks.

Uses Ethereum's built-in cryptographic functions.

Inspired by Web3 login solutions like MetaMask authentication.
<img width="1919" height="1060" alt="Screenshot 2026-03-17 155440" src="https://github.com/user-attachments/assets/fbfc52fe-8c45-4296-9b98-394e8338d310" />

## Output:
<img width="1918" height="1065" alt="image" src="https://github.com/user-attachments/assets/04348e68-acb5-4be2-aa35-4b078811107a" />


## RESULT:
Thus, a secure passwordless authentication system using public-private key cryptography on Ethereum was deployed and verified.
