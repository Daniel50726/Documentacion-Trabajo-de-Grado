# Technical Spec: 005 - Predictive Biometric Chart & Anomaly Visualizer

## Architecture & Data Flow

```mermaid
sequenceDiagram
    participant ML as Backend ML Service (Isolation Forest)
    participant API as Telemetry API Gateway
    participant Client as Client Application (Mobile / Web)
    participant Parser as TOON Tabular Decoder
    participant D3 as D3.js Render Engine

    ML->>API: Compute predictive corridor bounds (lower, upper, forecast)
    Client->>API: GET /api/v1/biometrics/timeseries/forecast?format=toon
    API-->>Client: 200 OK (Content-Type: text/plain; charset=utf-8) - TOON payload
    Client->>Parser: parseTabularToon(rawText)
    Parser-->>Client: Structured dataset { series: Array<DataPoint> }
    Client->>D3: render(container, series)
    D3->>D3: 1. Generate x/y scales (scalePoint / scaleLinear)
    D3->>D3: 2. Render predictive corridor path (d3.area)
    D3->>D3: 3. Construct outside-band clipPath (evenodd fill-rule)
    D3->>D3: 4. Draw base trajectory (blue layer: #779bc0)
    D3->>D3: 5. Draw clipped anomaly trajectory (red layer: #c9573b)
```

## Dual-Layer Vector Anomaly Masking Pattern

Rather than fragmenting line paths in JavaScript through expensive geometric intersections, the visualizer leverages hardware-accelerated SVG compositing:

1. **Predictive Corridor:**
   - Path generated via `d3.area()` mapping `x -> xScale(index)`, `y0 -> yScale(lower)`, and `y1 -> yScale(upper)` using `d3.curveMonotoneX`.
   - Filled with semi-transparent gray: `rgba(145, 145, 150, 0.22)`.

2. **Outside-Band ClipPath:**
   - An SVG `<clipPath id="outside-band">` is defined combining the bounding box rectangle with the corridor area path:
     ```svg
     <clipPath id="outside-band" clipPathUnits="userSpaceOnUse">
       <path d="M0,0H${width}V${height}H0Z ${area(series)}"
             fill-rule="evenodd"
             clip-rule="evenodd" />
     </clipPath>
     ```
   - Using the `evenodd` winding rule, the area enclosed by the predictive corridor is excluded, masking only pixels that fall strictly outside the expected bounds.

3. **Layer Stacking:**
   - **Base Layer (Blue):** `<g class="blue-line">` renders the continuous trajectory in `#779bc0` (stroke width 3px).
   - **Anomaly Layer (Red):** `<g class="red-line" clip-path="url(#outside-band)">` renders the identical line segments in `#c9573b`. Because it is masked by `outside-band`, the red line is only exposed for the out-of-band anomaly segments.

## Platform Implementation

### Web / Dashboard
- **Library:** D3.js v7 (`d3-scale`, `d3-shape`, `d3-axis`, `d3-selection`, `d3-transition`).
- **Container:** Dynamic SVG with `viewBox` and `overflow: visible` embedded in responsive flex/grid shells.
- **Resize Handling:** `ResizeObserver` / `window.addEventListener("resize")` with debounce triggering recalculation of scales without full DOM rebuild.

### Mobile App (iOS / Android)
- **Option A (Cross-platform WebView / Canvas):** Renders the optimized D3 module inside an isolated, high-performance WebView with hardware acceleration enabled.
- **Option B (Native Canvas / Jetpack Compose & SwiftUI):**
  - **Android (Compose):** Uses `Canvas` draw operations with `clipPath(Path.combine(Difference, fullRect, bandPath))` to overlay the red breach segments.
  - **iOS (SwiftUI / Metal):** Uses `Canvas` context with `.clip(to: path, style: FillStyle(eoFill: true))` applying `eoFill` (even-odd) masking.

## API Contracts & TOON Serialization

### Endpoint
- **URL:** `GET /api/v1/biometrics/timeseries/forecast`
- **Headers:**
  - `Accept: text/plain` (for TOON) or `application/json`
  - `Authorization: Bearer <JWT>`
- **Query Parameters:**
  - `metric`: string (`heart_rate` | `hrv` | `spo2`), default: `heart_rate`
  - `window`: string (e.g., `24h`, `7d`), default: `24h`
  - `format`: string (`toon` | `json`), default: `toon`

### TOON Payload Specification
TOON (Token-Oriented Object Notation) minimizes serialization overhead for resource-constrained clients by utilizing a compact tabular format:

```text
series[24]{time,value,lower,upper,type}:
"Tue 3",54,46,66,observed
"02:00",50,44,62,observed
"04:00",48,42,60,observed
"06:00",142,125,160,observed
"08:00",84,72,98,observed
"10:00",74,64,88,observed
"12:00",80,68,94,observed
"14:00",75,65,88,observed
"16:00",78,66,92,observed
"19:00",156,135,172,observed
"21:00",86,72,100,observed
"22:00",64,54,76,observed
"Wed 4",52,45,65,prediction
"02:00",49,43,62,prediction
"04:00",48,42,60,prediction
"06:00",138,120,158,prediction
"08:00",82,70,96,prediction
"10:00",73,63,86,prediction
"12:00",79,68,92,prediction
"14:00",74,64,88,prediction
"16:00",77,65,90,prediction
"19:00",154,132,170,prediction
"21:00",84,70,98,prediction
"22:00",63,53,75,prediction
```

### JSON Fallback Equivalent
```json
{
  "series": [
    {
      "time": "Tue 3",
      "value": 72,
      "lower": 60,
      "upper": 85,
      "type": "observed"
    },
    {
      "time": "12:00",
      "value": 115,
      "lower": 70,
      "upper": 100,
      "type": "prediction"
    }
  ]
}
```

## Data Schema (`DataPoint`)

| Field | Type | Required | Description | Constraints |
| :--- | :--- | :--- | :--- | :--- |
| `time` | String | Yes | Time label or ISO-8601 timestamp string | e.g., `"12:00"`, `"2026-11-04T12:00:00Z"` |
| `value` | Number | Yes | Observed or predicted biometric metric value | Value in BPM (e.g., `40` to `220`) |
| `lower` | Number | Yes | Lower confidence bound of predictive model | `lower <= value` (under normal conditions) |
| `upper` | Number | Yes | Upper confidence bound of predictive model | `upper >= lower` |
| `type` | String | Yes | Classification of point origin | Enum: `"observed"` \| `"prediction"` |
| `is_anomaly` | Boolean | Calculated | Computed dynamically on client or server | `true` if `value < lower` or `value > upper` |

## Prototype Reference
A fully functional standalone prototype implementing the D3 SVG render pipeline, tabular TOON parser, and animated out-of-bounds clip masking is located at:
[`specs/features/005-toon-and-chart-render/example/toon_and_chart_render.html`](example/toon_and_chart_render.html)
