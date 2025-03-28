# Image Metadata Challenge

## Basic Idea and Topic
This CTF challenge involves an ordinary-looking image with a secret flag hidden in its metadata. The flag is encrypted within the EXIF `UserComment` field, and the decryption key is embedded in the `Copyright` field. Participants must extract the metadata, identify the encrypted flag, and decrypt it using the Vigenère cipher to reveal `flag{i_am_groot!}`. The challenge tests metadata analysis, steganography awareness, and basic cryptographic skills.

## Trap
Participants might fall into the trap of analyzing the image's pixels for steganographic clues (e.g., hidden patterns or LSB encoding), overlooking the metadata entirely. The flag is not in the visible content but concealed in the metadata, requiring forensic tools like `exiftool` rather than image viewers or editors.

## Correct Solution
To solve the challenge:

1. **Download the Image**: Access the image via the provided link on the web page.
2. **Extract Metadata**: Use a tool like `exiftool`:
   ```bash
   exiftool image.jpg
   ```
   Output includes:

   `UserComment: xpcx{m_to_gdsft!}  
   Copyright: Copyright 2023 SecretCamera Inc.`
3. **Identify the Encrypted Flag**: The `UserComment` field contains a strange string (`xpcx{m_to_gdsft!}`), suggesting it’s the encrypted flag.
4. **Find the Key**: In the `Copyright` field, the phrase `SecretCamera` is embedded within `Copyright 2023 SecretCamera Inc.`. This is  not a real copyright holder (or a company) and is our decryption key.
5. **Determine the Encryption Method**: The encrypted text doesn’t resemble Base64 or simple ciphers like Caesar; its polyalphabetic nature hints at Vigenère cipher, especially given the key length and flag format.
6. **Decrypt the Flag**: Use the Vigenère cipher with key SecretCamera (case-insensitive here, as the flag is lowercase):  
  -*Ciphertext*: `xpcx{m_to_gdsft!}`  
  -*Key*: `secretcamera`  
  -*Decryption* (subtract key shifts, mod 26):
   
      `x(23) - s(18) = 5 → f`  
      `p(15) - e(4) = 11 → l`  
      `c(2) - c(2) = 0 → a`  
      `x(23) - r(17) = 6 → g`  
      `{` unchanged  
      `m(13) - e(4) = 9 → i`  
      ... (continues similarly)
   
     -*Result*: `flag{i_am_groot!}`  

**Flag**: `flag{i_am_groot!}`

**Tools Recommended**: `exiftool` for metadata extraction; any Vigenère cipher tool or script for decryption.

## Deployment
1. **Build the Docker Image**:
```bash
docker build -t image-metadata-challenge .
```
2. **Run the Container**:
```bash
docker run -p 80:80 image-metadata-challenge
```
3. **Access**: Open `http://localhost` in a browser to see the challenge page and download the image.

## Python Script for Encryption
The following Python script can be used to encrypt the flag using the Vigenère cipher:

```python
def vigenere_encrypt(plaintext, key):
    key = key.lower()
    key_index = 0
    ciphertext = ''
    for char in plaintext:
        if char.isalpha():
            shift = ord(key[key_index]) - ord('a')
            if char.isupper():
                ciphertext += chr((ord(char) - ord('A') + shift) % 26 + ord('A'))
            else:
                ciphertext += chr((ord(char) - ord('a') + shift) % 26 + ord('a'))
            key_index = (key_index + 1) % len(key)
        else:
            ciphertext += char
    return ciphertext

if __name__ == "__main__":
    flag = "flag{i_am_groot!}"
    key = "SecretCamera"
    encrypted_flag = vigenere_encrypt(flag, key)
    print(f"Encrypted Flag: {encrypted_flag}")
```

### Example Output
Running the script:
```bash
python encrypt.py
```
Output:
```
Encrypted Flag: xpcx{m_to_gdsft!}
```

This script demonstrates how the flag was encrypted.