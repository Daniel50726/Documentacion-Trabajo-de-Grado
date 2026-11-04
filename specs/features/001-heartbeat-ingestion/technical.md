# Technical Spec: 001 - Heartbeat Ingestion from Smartwatch

## Architecture & Data Flow
`Wristband (BLE Peripheral)` -> `Mobile App (BLE Central)` -> `SQLite / Room DB`

## Implementation Details
- **iOS:** CoreBluetooth framework utilizing `CBCentralManager` and `CBPeripheral`.
- **Android:** Android BLE API utilizing `BluetoothGatt` and background `ForegroundService`.
- **Data Retention:** Raw BPM readings are throttled via a 5-second sliding window average before being written to local persistence to save disk space.

## Local Database Schema (`heart_rate_events`)

| Column | Type | Description |
| :--- | :--- | :--- |
| `id` | INTEGER (PK) | Auto-incrementing identifier |
| `timestamp` | INTEGER | Unix timestamp in milliseconds |
| `bpm` | INTEGER | Heart rate values |
| `sync_status` | TEXT | PENDING, SYNCED, FAILED |
