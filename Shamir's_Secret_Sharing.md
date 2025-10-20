# Shamir's Secret Sharing

## Challenge Overview
This challenge involves recovering 5 shares from different file formats and using threshold cryptography (specifically Shamir's Secret Sharing) to reconstruct an AES key for decrypting a vault file.

## Solution Walkthrough

### 1. Share Recovery

#### Share 1: share1.txt
- **File**: `share1.txt`
- **Content**: Direct text containing the share value
- **Share**: `(1, 201522632269176227792529259658745111658)`

#### Share 2: share2.png
- **File**: `share2.png`
- **Process**:
  1. Extract QR code from the image
  2. Apply Caesar cipher with shift of 5 across ASCII
  3. Decode the result
- **Share**: `(2, 224770860677244800791621415404633393675)`

#### Share 3: share3/encrypted_message.bin
- **File**: `share3/encrypted_message.bin`
- **Process**:
  1. Use RSA-2048 private key from `private_key.pem`
  2. Decrypt the binary file
- **Share**: `(3, 135324715440419453670163953081590813641)`

#### Share 4: share4.zip
- **File**: `share4.zip`
- **Process**:
  1. Extract using password `ctf_2025` (from SecretKey)
  2. Read the extracted content
- **Share**: `(4, 120473893289346312314482711088749854173)`

#### Share 5: share5.pdf
- **File**: `share5.pdf`
- **Process**:
  1. Open with password `ctf_2025`
  2. Inspect metadata using `qpdf --decrypt`
  3. Find UTF-16 hex payload
  4. Apply Vigenère cipher decryption (mod 128) using key `ctf_2025`
  5. Decode the result from base64
- **Share**: `(5, 25019457618722413877035949434840599806)`

### 2. AES Key Reconstruction

#### Prime Modulus
- **Prime**: `309465533233587653298203953963739275397`
- **Source**: `ctf/final/prime`

#### Lagrange Interpolation
Using the 5 recovered shares `(x, y)` where `x = 1, 2, 3, 4, 5`, we perform Lagrange interpolation at `x = 0` (mod p) to recover the secret.

**Shares**:
```
(1, 201522632269176227792529259658745111658)
(2, 224770860677244800791621415404633393675)
(3, 135324715440419453670163953081590813641)
(4, 120473893289346312314482711088749854173)
(5, 25019457618722413877035949434840599806)
```

**Reconstructed Secret**: `thresh0ld_k3y_05` (16 bytes, suitable for AES-128)

### 3. Vault Decryption

#### File Structure
- **File**: `vault.enc`
- **Format**: Base64 encoded data containing `IV || ciphertext`

#### Decryption Process
1. **IV**: Fixed 16-byte IV: `"fixed_iv_16bytes"`
2. **Cipher**: AES-128-CBC
3. **Key**: `thresh0ld_k3y_05` (reconstructed from shares)
4. **Result**: `CSCV2025{Thresh0ld_c1pher_2025@?}`

## Technical Details

### Shamir's Secret Sharing
This challenge implements Shamir's Secret Sharing scheme where:
- A secret is split into 5 shares using a polynomial of degree 4
- Any 5 shares can reconstruct the original secret
- The secret is reconstructed using Lagrange interpolation

### Cryptographic Components
- **RSA-2048**: Used for encrypting share 3
- **Caesar Cipher**: Used for encoding share 2
- **Vigenère Cipher**: Used for encoding share 5
- **AES-128-CBC**: Used for final vault encryption
- **Base64**: Used for encoding various components

## Tools Used
- QR code reader for share 2
- RSA decryption tools for share 3
- PDF analysis tools (qpdf) for share 5
- Lagrange interpolation implementation
- AES decryption tools

## Flag
```
CSCV2025{Thresh0ld_c1pher_2025@?}
```

## Key Takeaways
1. **Threshold Cryptography**: Understanding how Shamir's Secret Sharing works
2. **Multi-format Analysis**: Working with various file formats and encodings
3. **Cryptographic Chaining**: Combining multiple cryptographic primitives
4. **Data Recovery**: Extracting information from different sources and formats

This challenge demonstrates the practical application of threshold cryptography in a CTF setting, requiring knowledge of multiple cryptographic techniques and file format analysis.
