# Functional Spec: 005 - Predictive Biometric Chart & Anomaly Visualizer

## User Story
**As a** health-conscious user monitoring my cardiovascular trends, **I want** the client application to render my real-time heart rate alongside my historical predictive baseline band **so that** I can immediately identify when my physiological measurements deviate into anomalous territory.

## Description
The client frontend (mobile application and web dashboard) provides an interactive, high-performance time-series visualizer for heart rate and related biometric signals. The visualizer translates complex machine learning predictions into an intuitive graphical interface.

The visualization relies on two primary graphic elements:
1. **Predictive Baseline Corridor (Gray Band):** A shaded area bounded by dynamic upper and lower thresholds (`lower` and `upper`), derived from historical machine learning modeling (Isolation Forest / baseline forecast). This band defines the expected normal physiological range for the user at any given time.
2. **Current Biometric Trajectory:** A continuous line plotting the user's actual recorded measurements.

### Visual Anomaly Trigger
An anomaly is triggered whenever the actual biometric value breaches the boundaries of the predictive corridor:
- **Normal Range:** Values lying within the gray corridor render in standard calm blue (`#779bc0`).
- **Anomalous Breach:** Any line segment or data point that exceeds the upper threshold (tachycardia / spike) or drops below the lower threshold (bradycardia / sudden drop) immediately renders in warning red (`#c9573b`).

The visualizer supports compact tabular TOON (Token-Oriented Object Notation) encoding to optimize bandwidth consumption over mobile and Bluetooth connections.

## Acceptance Criteria
- The chart must render the predictive model as a continuous shaded gray band (`rgba(145,145,150,0.22)`) bounded by `lower` and `upper` confidence limits.
- The actual biometric measurements must be plotted chronologically against the predictive baseline.
- Line segments located inside the gray band must display in standard blue (`#779bc0`).
- Line segments extending outside the gray band must render in alert red (`#c9573b`) using clip-path masking or vector segment partitioning.
- An anomaly must be triggered in real time when `current_value > upper_bound` or `current_value < lower_bound`.
- The chart shell must be responsive, recalculating SVG viewports and scales automatically upon screen resize or device rotation.
- The chart parser must support decoding tabular TOON payloads (`series[N]{time,value,lower,upper,type}:`) as demonstrated in the prototype example.

## Scenarios (BDD Gherkin)

```gherkin
Feature: Predictive Biometric Chart & Anomaly Visualizer
  As a health-conscious user monitoring my cardiovascular trends
  The application renders real-time telemetry against predictive baseline corridors
  To provide immediate visual clarity on cardiac anomalies

  Scenario: Rendering normal heart rate within the predictive corridor
    Given the predictive baseline band is defined by lower and upper heart rate thresholds
    When the current heart rate measurements remain within the expected corridor
    Then the telemetry trajectory is rendered in standard blue
    And no anomaly alert state is indicated on the chart

  Scenario: Highlighting a tachycardia spike exceeding the upper predictive boundary
    Given the predictive baseline corridor has an upper threshold of 100 BPM
    When the user heart rate spikes above 100 BPM
    Then the segment exceeding the threshold immediately transitions to warning red
    And the visualizer highlights the breach as an upper anomalous event

  Scenario: Highlighting a bradycardia drop falling below the lower predictive boundary
    Given the predictive baseline corridor has a lower threshold of 50 BPM
    When the user heart rate drops below 50 BPM
    Then the segment below the lower threshold immediately transitions to warning red
    And the visualizer highlights the breach as a lower anomalous event

  Scenario: Recovering to normal state when measurements return into the corridor
    Given the heart rate line is currently rendering in warning red outside the corridor
    When subsequent telemetry readings re-enter the boundaries of the gray band
    Then the new line segments return to standard blue
    And the anomalous state terminates visually

  Scenario: Maintaining responsive scale on screen resize or device orientation change
    Given the biometric chart is actively rendering telemetry data
    When the user resizes the window or rotates their mobile device
    Then the SVG viewbox and axis scales recalculate proportionally
    And all trajectory coordinates and predictive bands adapt without distortion

  Scenario: Ingesting and rendering compact tabular TOON telemetry
    Given the backend delivers time-series data encoded in tabular TOON format
    When the client chart parser processes the payload
    Then the time, value, lower, upper, and type attributes are mapped accurately
    And the predictive baseline band and observed trajectory are drawn without data loss
```

