Domain Snapshot

This project focuses on the analysis of motion data collected from IoT devices equipped with inertial sensors. Each device continuously generates sensor readings while the user is in motion, such as during walking activities. The system is designed to answer questions such as:

How intense is the user’s movement over time?

When do peaks of activity or abrupt movements occur?

Which periods correspond to stable or low-movement behaviour?

How do acceleration, rotation, gravity and orientation relate to each other?

The solution must efficiently store high-frequency sensor data and support complex analytical queries.

Collections
Collection	Role	Notes
motion_data	Fact / telemetry data	Each document represents a single sensor reading with embedded motion and orientation data.
Schema Highlights
// motion_data
{
  _id: ObjectId,
  userAcceleration: {
    x: Number,
    y: Number,
    z: Number
  },
  rotationRate: {
    x: Number,
    y: Number,
    z: Number
  },
  gravity: {
    x: Number,
    y: Number,
    z: Number
  },
  attitude: {
    pitch: Number,
    roll: Number,
    yaw: Number
  }
}

Modeling Decisions

Single collection for sensor readings
Each sensor measurement is stored as an independent document. This approach matches the nature of IoT data, where readings are generated frequently and do not require transactional relationships.

Embedded documents for vector data
Acceleration, rotation, gravity and orientation are stored as embedded objects with x, y and z components. This keeps related values together and simplifies aggregation pipelines that compute magnitudes or statistical metrics.

No normalization or cross-collection references
Since the project focuses on motion analysis rather than user or device management, all relevant data is stored within a single collection. This avoids joins and improves query performance.

Optimised for analytical workloads
The data model prioritises read-heavy analytical queries, such as filtering by thresholds, computing averages, percentiles and detecting extreme values.

Relationships & Access Patterns

There are no inter-collection relationships in the current model.

Most queries scan or aggregate documents within motion_data.

Common access patterns include:

Filtering by acceleration or rotation thresholds.

Computing derived fields such as acceleration or rotation magnitude.

Grouping all documents to calculate global statistics.

Sorting by computed values to identify extreme movement events.

Index Blueprint

Given the analytical nature of the queries, indexing strategies focus on improving filtering and sorting operations:

Optional index on acceleration components:
{ "userAcceleration.x": 1, "userAcceleration.y": 1, "userAcceleration.z": 1 }

Optional index on rotation components:
{ "rotationRate.x": 1, "rotationRate.y": 1, "rotationRate.z": 1 }

These indexes can help optimise queries that detect high acceleration or rotation values. Aggregation-heavy queries that compute derived fields are primarily CPU-bound and benefit more from the document structure than from extensive indexing.

Indexes can be created through dedicated scripts to allow easy re-creation after data reloads.

Summary

This architecture adopts a simple and efficient document-based data model that aligns with real-world IoT sensor data. By storing each sensor reading as a self-contained document and leveraging MongoDB’s aggregation framework, the system supports flexible, high-performance analysis of motion patterns, making it suitable for wearable devices, mobile sensors and other IoT scenarios.