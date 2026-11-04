# Technical Spec: 004 - Predictive Anomaly Detection [WIP]

## Machine Learning Stack
- **Model Type:** Isolation Forest / LSTM Autoencoder (depending on training compute budgets).
- **Inference Pipeline:** Python (FastAPI) backend running `scikit-learn` or `TensorFlow Serving`.

## Predictive Pipeline Flow
1. Backend receives time-series telemetry via Sync Engine.
2. Chronological batching inputs data into the user-specific ML profile model.
3. If an anomaly is computed:
    - Log entry inside backend MongoDB `anomalies` collection.
    - Dispatch payload to Firebase Cloud Messaging (FCM) / Apple Push Notification service (APNs).
