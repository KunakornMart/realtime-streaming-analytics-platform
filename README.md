# Real-Time Data Streaming & Analytics Platform

<p align="center">
  <b>Apache Kafka</b> · <b>ksqlDB</b> · <b>Apache Pinot</b> · <b>Streamlit</b> · <b>Python</b>
</p>

<p align="center">
  <i>DADS6005, NIDA · Aug – Oct 2024</i>
</p>

---

## Overview

This project demonstrates a full **real-time analytics pipeline** built with **Apache Kafka**, **ksqlDB**, **Apache Pinot**, and **Streamlit**. The goal was to build an end-to-end system that actually runs as a complete workflow, from ingestion and stream processing to storage and dashboarding.

The platform ingests multiple data sources, processes them through SQL-based stream transformations and window functions in **ksqlDB**, stores the outputs in **Apache Pinot** for fast analytical queries, and serves the results through a continuously updating **Streamlit** dashboard.

---

## Architecture at a Glance

```text
Data Sources (3)
    ↓
Kafka (8 topics, 3 brokers, 5 partitions/topic)
    ↓
ksqlDB (transform, join, tumbling / hopping / session windows)
    ↓
Apache Pinot (real-time OLAP queries)
    ↓
Streamlit Dashboard (live refresh + interactive filters)
```

---

## What the Pipeline Handles

### Data Sources

The system uses three data sources:

- **Page views** — simulated user activity events with fields such as `viewtime`, `userid`, and `pageid`
- **User demographics** — user records with `userid`, `regionid`, `gender`, and `registertime`
- **Regional reference data** — a custom dataset containing province names, population, and area size, generated through a Python script

Sources 1 and 2 use Kafka's Datagen connector. Source 3 is a static relational dataset produced separately with Python and fed into the pipeline.

### Kafka Layer

The Kafka cluster runs with:

- **3 brokers**
- **5 partitions per topic**
- **Schema Registry** for schema consistency across topics

There are **8 topics** in total:

- **topic1–topic3** for raw ingestion
- **topic4–topic8** for processed and transformed outputs from ksqlDB

### Stream Processing with ksqlDB

Most of the transformation work happens in **ksqlDB**. The pipeline is split into several stages:

- **topic4** — timestamp formatting and cleanup on the user stream
- **topic5** — enriched stream created by joining page views, user demographics, and regional data
- **topic6** — tumbling window aggregation for page-view counts in 1-minute non-overlapping windows
- **topic7** — hopping window aggregation every 5 seconds for short-term trend monitoring
- **topic8** — session window analysis for per-user session length based on inactivity gaps

### Apache Pinot

**Apache Pinot** serves as the real-time OLAP layer. It ingests from Kafka continuously and supports low-latency SQL queries, which makes it a good fit for dashboards that need frequent updates.

One practical takeaway from this project: Pinot takes more setup than simpler time-series databases, but once the schemas and tables are in place, it is much more flexible for real-time analytical querying.

### Streamlit Dashboard

The **Streamlit** dashboard queries Pinot and refreshes continuously. It includes:

- Total page visits by province/region
- Average time on page by province
- Average session length by user
- Rolling 5-minute page-visit trend
- Interactive filters for region and gender

---

## Key Features

- Full end-to-end pipeline from ingestion to dashboard
- Real-time join across three data sources
- Window-based analytics using **tumbling**, **hopping**, and **session** windows
- Low-latency analytical queries powered by **Apache Pinot**
- Continuously updating **Streamlit** dashboard with interactive filtering

---

## Tech Stack

| Layer | Tools / Components |
|------|---------------------|
| Data ingestion | Kafka Datagen, Python producer |
| Streaming transport | Apache Kafka |
| Stream processing | ksqlDB |
| Analytics storage | Apache Pinot |
| Dashboard | Streamlit |
| Language | Python |
| Deployment | Docker, Docker Compose |

---

## Project Structure

```text
realtime-streaming-analytics-platform/
├── docker-compose.yml
├── README.md
├── source_code_6610422020
├── report_6610422020
├── source3_data.py
├── streamlit_app.py
├── kafka/
├── ksqldb/
├── pinot/
└── assets/
```

> Adjust the structure above if your local repository layout is slightly different.

---

## Setup

### Prerequisites

- Docker
- Docker Compose

### Run the project

```bash
git clone https://github.com/KunakornMart/realtime-streaming-analytics-platform.git
cd realtime-streaming-analytics-platform
docker-compose up -d
```

### Notes

- Main implementation details are in `source_code_6610422020`
- Additional design explanation and pipeline notes are in `report_6610422020`

---

## Dashboard Preview

### Main dashboard

![Dashboard Overview](https://drive.google.com/uc?export=view&id=1YxHnrM5-Tuw-kVxvkq2Uyk8GJs0ojJgW)

### Animated demo

![Video Demonstration](https://github.com/KunakornMart/DADS6005/blob/main/Realtime-Video.gif?raw=true)

---

## Live Demo

| Service | URL |
|:--------|:----|
| Streamlit Dashboard | http://ec2-47-129-89-174.ap-southeast-1.compute.amazonaws.com:8501 |
| Confluent Control Center | http://ec2-47-129-89-174.ap-southeast-1.compute.amazonaws.com:9021 |
| Apache Pinot Console | http://ec2-47-129-89-174.ap-southeast-1.compute.amazonaws.com:9000 |

> The EC2 instance used during the course is no longer running, but the code and architecture are still included in this repository.

---

## Why This Project Matters

This project was useful because it ties together the parts that are often shown separately:

- ingestion
- stream processing
- real-time aggregation
- analytical querying
- dashboard delivery

Instead of building a single isolated component, the project shows how a real-time analytics stack can work as one connected system.

---

## What I’d Improve Next

- **More realistic data sources** — adding IoT streams or external APIs would make the joins and downstream analysis more meaningful than simulated input alone
- **Predictive layer** — the current system handles streaming aggregation well, but adding a forecasting or anomaly-detection layer would be a natural extension
- **Production-grade dashboarding** — Streamlit worked well for demonstration, but tools like Grafana or Superset would offer more flexibility for larger deployments
- **Partition strategy tuning** — 5 partitions worked at this scale, but higher traffic would need a more deliberate partitioning approach

---

## Skills Demonstrated

- Real-time data pipeline design
- Stream processing with SQL
- Kafka topic and broker orchestration
- Windowed analytics
- Low-latency OLAP querying
- Interactive dashboard development
- Docker-based service orchestration

---

## Author

**Kunakorn Pruksakorn**  
M.Sc. in Data Analytics and Data Science, NIDA

---

<p align="center">
  Built as part of the M.Sc. in Data Analytics and Data Science (DADS6005), NIDA, Thailand.
</p>
