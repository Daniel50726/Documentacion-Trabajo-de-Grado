# Technical Spec: 003 - Background Sync Engine [WIP]

## API Contracts
- **Endpoint:** `POST /api/v1/biometrics/sync`
- **Headers:** `Authorization: Bearer <JWT_TOKEN>`

### Request Payload Example
```json
{
  "device_id": "urn:uuid:f81d4fae-7dec-11d0-a765-00a0c91e6bf6",
  "payload": [
    {"t": 1793746800, "b": 72},
    {"t": 1793746805, "b": 75}
  ]
}
```

## Scheduling Workers
- **Android:** `WorkManager` API with constraints: `NetworkType.CONNECTED`, `RequiresBatteryNotLow()`.
- **iOS:** `BGProcessingTaskRequest` from the BackgroundTasks framework.
