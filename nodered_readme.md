## Node-RED

Serving as the central communication hub between MQTT-enabled sensors, a PostgreSQL database, and the Grafana visualization suite.

### Project Overview

**Port:** `1880`
**Node-RED Version:** `4.1.5`
**Core Purpose:** Facilitates sensor data ingestion, database persistence, and measurement control logic.

---

### Dependencies
To run this project, the following nodes must be installed via the Palette Manager or `npm`:

| Node Package | Version | Description |
| :--- | :--- | :--- |
| `@flowfuse/node-red-dashboard` | `1.30.2` | Primary UI for device control and data visualization. |
| `node-red-contrib-postgresql` | `0.15.4` | Handles all queries and insertions to the database. |

---

### Development Practices
To maintain project integrity and readability:

* **Concurrency:** **One person, one flow.** To avoid merge conflicts, only work on a single flow at a time.
* **Readability:** Use **Comments** to label MQTT links and logic blocks clearly.
* **Organization:** Use **Tabs/Flows** to group relevant logic blocks (e.g., separating Database logic from UI logic).
* **Version Control:** Before committing, export the workspace: 
    * `Export` -> `All Flows` -> `JSON` -> Save as `flows.json`.

---

## Flow Architecture

### 1. MQTT Reception
The entry point for all sensor data. Messages from Mosquitto are filtered into standardized internal links for clean downstream consumption:
* `[heartbeat]`, `[status]`, `[start]`, `[stop]`, `[data]`, `[error]`

### 2. Database Integration
The central page for data persistence. This flow parses sensor messages and maps them to specific PostgreSQL tables:
* **Measurements:** Triggered by `[start]`, `[stop]`, and `[status]`.
* **Data:** Maps `[data]` to `typedata` (flow/gas) and then to the `data` table.
* **Errors:** Maps `[error]` to the `error` table.
* *Handles: Queries, sequences, and ungraceful disconnect logic.*

### 3. Logic & Control
* **Device Status Control:** Logic for measurement lifecycle (Start/Stop) and Dashboard GUI label updates.
* **MQTT Endpoints:** A debug/monitoring flow to display all MQTT traffic (excluding errors).
* **Heater Profiles & Sequences:** Tools to query existing profiles from the DB and construct new heater profile sequences.

---

## Dashboard Navigation
The FlowFuse Dashboard is categorized into the following functional pages:

| Page | Description |
| :--- | :--- |
| **Device Status Control** | Main UI to start and stop physical measurements. |
| **Grafana** | Embedded view of the Grafana instance for real-time graphing. |
| **Measurements** | Access to historical measurement logs and a quick-link to the **IRA-Tool**. |
| **Heater Profiles** | Management interface for heating profile configurations. |
| **Sequences** | Management interface for making heater profile sequences. |
| **MQTT-Endpoints** | Overview of incoming mqtt messages. |
| **Error** | Dedicated page for monitoring system and sensor error messages. |


> **Note:** Currently no folder mounted to the container, so version control is very manual.
