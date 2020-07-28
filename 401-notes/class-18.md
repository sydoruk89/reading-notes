# Cryptography
## Polymorphism
Polymorphism is basically a cipher that changes itself with each use. Meaning that each time it is used, it produces a different set of results. So, if you encrypted the exact same set of data twice, each new encryption would be different from the previous one.

## Types of Cryptography
They are called: 
- hashing; 
- symmetric cryptography; 
- asymmetric cryptography;
- key exchange algorithms.

## 1. Hashing
Hashing is a type of cryptography that changes a message into an unreadable string of text for the purpose of verifying the message’s contents, not hiding the message itself. \
Currently, the most common hashing algorithms are MD5 and SHA-1, however due to these algorithm’s multiple weaknesses, most new applications are transitioning to the SHA-256 algorithm instead of its weaker predecessors.

## 2. Symmetric Cryptography
This type of cryptography uses a single key to encrypt a message and then decrypt that message upon delivery.
Most modern symmetric cryptography relies on a system known as AES or Advanced Encryption Standards.

## 3. Asymmetric Cryptography
Asymmetric cryptography (as the name suggests) uses two different keys for encryption and decryption, as opposed to the single key used in symmetric cryptography. \
The first key is a public key used to encrypt a message, and the second is a private key which is used to decrypt them. The great part about this system is that only the private key can be used to decrypt encrypted messages sent from a public key. \
It is used when transmitting email files, remotely connecting to servers, and even digitally signing PDF files. Oh, and if you look in your browser and you notice a URL beginning with “https://”, that’s a prime example of asymmetric cryptography keeping your information safe.

## 4. Key Exchange Algorithms
A key exchange algorithm, like Diffie-Hellman, is used to safely exchange encryption keys with an unknown party. \
Unlike other forms of encryption, you are not sharing information during the key exchange. The end goal is to create an encryption key with another party that can later be used with the aforementioned forms of cryptography.