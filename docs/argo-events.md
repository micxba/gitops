🧩 Argo Events Architecture Overview
Argo Events consists of four main components:

1. EventSource
Defines how to listen to external events like:

GitHub webhook pushes

Cron schedules

Kafka/NATS messages

S3 uploads, etc.

🧠 Think of this as the "listener".

2. Sensor
Listens for events from one or more EventSources and triggers:

An Argo Workflow

A Kubernetes resource (like a Job)

Or multiple resources

🧠 Think of this as the "router + logic layer".

3. EventBus
This is the transport layer between EventSources and Sensors.

By default, Argo Events supports:

🟩 NATS JetStream (default, lightweight pub/sub)

🟦 Kafka (for enterprise-scale messaging)

🟨 Generic (bring your own transport)

🧠 The EventBus ensures reliable delivery of events to sensors.