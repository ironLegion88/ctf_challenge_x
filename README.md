# Image Metadata Challenge

## Basic Idea and Topic
This CTF challenge involves an ordinary-looking image with a secret flag hidden in its metadata. The flag is encrypted within the EXIF `UserComment` field, and the decryption key is embedded in the `Copyright` field. Participants must extract the metadata, identify the encrypted flag, and decrypt it using the Vigenère cipher to reveal `flag{metadata_secrets}`. The challenge tests metadata analysis, steganography awareness, and basic cryptographic skills.

## The Catch / Trap
Participants might fall into the trap of analyzing the image's pixels for steganographic clues (e.g., hidden patterns or LSB encoding), overlooking the metadata entirely. The flag is not in the visible content but concealed in the metadata, requiring forensic tools like `exiftool` rather than image viewers or editors.

## Correct Solution
To solve the challenge:

1. **Download the Image**: Access the image via the provided link on the web page.
2. **Extract Metadata**: Use a tool like `exiftool`:
   ```bash
   exiftool image.jpg
   ```
   Output includes:

   `UserComment: xpcx{qxvapeka_kiemu}
   Copyright: Copyright 2023 SecretCamera Inc.`
3. **Identify the Encrypted Flag**: The `UserComment` field contains a strange string (`xpcx{qxvapeka_kiemu}`), suggesting it’s the encrypted flag.
4. **Find the Key**: In the `Copyright` field, notice `SecretCamera` within `Copyright 2023 SecretCamera Inc.`. This is an unusual term and likely the decryption key.
5. **Determine the Encryption Method**: The encrypted text doesn’t resemble Base64 or simple ciphers like Caesar; its polyalphabetic nature hints at Vigenère cipher, especially given the key length and flag format.
6. **Decrypt the Flag**: Use the Vigenère cipher with key SecretCamera (case-insensitive here, as the flag is lowercase):  
  -*Ciphertext*: `xpcx{qxvapeka_kiemu}`  
  -*Key*: `secretcamera` (repeated: `secretcamerasecretc` for 19 letters)  
  -*Decryption* (subtract key shifts, mod 26):
   
      `x(23) - s(18) = 5 → f`  
      `p(15) - e(4) = 11 → l`  
      `c(2) - c(2) = 0 → a`  
      `x(23) - r(17) = 6 → g`  
      `{` unchanged  
      `q(16) - e(4) = 12 → m`  
      ... (continues similarly)
   
  -*Result*: `flag{metadata_secrets}`  
8. **Verify**: The decrypted text matches the CTF flag format.

**Flag**: `flag{metadata_secrets}`

**Tools Recommended**: `exiftool` for metadata extraction; any Vigenère cipher tool or script for decryption.

## Deployment
1. **Prepare Files**: Ensure `html/image.jpg` has the metadata set as described.
2. **Build the Docker Image**:
```bash
docker build -t image-metadata-challenge .
```
3. **Run the Container**:
```bash
docker run -p 80:80 image-metadata-challenge
```
4. **Access**: Open `http://localhost` in a browser to see the challenge page and download the image.
