# Technical Spec: 002 - Secure Local Data Persistence

## Technologies
- **Android:** Room DB backed by **SQLCipher** for database encryption.
- **iOS:** SwiftData or GRDB with SQLite Encryption Extension (SEE).
- **Encryption Key Management:** Keys are dynamically generated on the first boot and stored securely inside the hardware-backed keystores (**Android Keystore** / **iOS Keychain**).

## Security Verification
- Database files fetched via ADB or Device File Explorer must be completely unreadable without the AES-256 decryption key.
