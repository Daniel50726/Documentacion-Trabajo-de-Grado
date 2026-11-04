# Functional Spec: 001 - Heartbeat Ingestion from Smartwatch

## User Story
**As a** health-conscious user wearing the AnomaPulse wristband, **I want** the mobile application to continuously read my heart rate data in the background **so that** my biometric baseline can be established without manual interaction.

## Description
The mobile app must establish a low-energy Bluetooth (BLE) connection with the wristband. It will subscribe to the Heart Rate Service (UUID `0x180D`) and receive live beats-per-minute (BPM) updates.

## Acceptance Criteria
- The app must auto-reconnect to the paired wristband if the connection drops.
- A persistent background notification must display to show the app is actively monitoring.
- The UI must show a real-time pulse indicator on the dashboard when the wristband is connected.

## Scenarios (BDD Gherkin)

```gherkin
Feature: Heartbeat Ingestion from Smartwatch
  As a health-conscious user wearing the AnomaPulse wristband
  The mobile application continuously collects heart rate telemetry in the background
  To maintain an accurate biometric baseline

  Scenario: Continuous ingestion of heart rate updates over Bluetooth
    Given the mobile application is connected to a paired wristband
    When the wristband transmits new heart rate measurements
    Then the application records the biometric data in real time
    And updates the live pulse rate indicator on the user dashboard

  Scenario: Automatic reconnection after unexpected connection loss
    Given the mobile application is actively monitoring heart rate from a connected wristband
    When the Bluetooth signal is unexpectedly lost
    Then the application initiates background reconnection attempts
    And notifies the user that the sensor is temporarily disconnected

  Scenario: Re-establishing telemetry stream upon reconnection
    Given the mobile application is attempting to reconnect to a previously paired wristband
    When the wristband comes back within Bluetooth range
    Then the connection is restored automatically
    And live heart rate streaming resumes without user intervention

  Scenario: Background service visibility
    Given the mobile application is running with active background monitoring
    When the user minimizes the application
    Then a persistent system notification remains visible
    And the notification displays the current monitoring status
```
