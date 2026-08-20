# Medallion Data Platform

A cloud data platform built with **AWS CDK (Python)** that implements the **Medallion architecture** (Bronze → Silver → Gold) to ingest, process, store, and visualize data from external sources such as **Hacker News** and **Twitter**.

> University team project — Cloud Computing course. This is a fork of the original team repository ([dkomatovic/medallion-data-platform](https://github.com/dkomatovic/medallion-data-platform)), kept here as part of my project portfolio.

## Architecture

The platform is organized into three data layers, each backed by AWS Lambda functions:

- **Bronze** — raw ingestion from external APIs (Hacker News, Twitter) into landing storage.
- **Silver** — cleaning and normalization of the raw data.
- **Gold** — curated, aggregated data ready for analytics and reporting.

From the Gold layer, data is synced into a **PostgreSQL** database (`sync/gold_to_postgres`) running on **EC2**, which powers an **Apache Superset** dashboard for visualization. A **Discord** notification Lambda alerts on pipeline events.

```
Sources (Hacker News, Twitter)
        │
        ▼
  Bronze Lambdas  →  Silver Lambdas  →  Gold Lambdas
                                            │
                                            ▼
                              PostgreSQL (EC2) → Superset dashboards
                                            │
                                            ▼
                                  Discord notifications
```

## Tech stack

- **Infrastructure as Code:** AWS CDK (Python)
- **Compute:** AWS Lambda, EC2
- **Storage:** PostgreSQL
- **Visualization:** Apache Superset
- **Containerization:** Docker (for lambdas with heavier dependencies, e.g. Twitter ingestion)
- **Notifications:** Discord webhook integration

## Project structure

```
medallion_data_platform/   # CDK stack and constructs (networking, notifications, visualization)
lambdas/
  bronze/                  # raw ingestion (hacker_news, twitter)
  silver/                  # cleaning & normalization
  gold/                    # curated datasets
  sync/                    # gold_to_postgres sync job
  notify/                  # discord notifications
ec2/                       # EC2 user data, Superset config, DB init scripts
tests/                     # unit tests
app.py                     # CDK app entry point
```

## Getting started

```bash
python -m venv .venv
.venv\Scripts\activate.bat        # Windows
pip install -r requirements.txt

cdk synth      # synthesize the CloudFormation template
cdk deploy     # deploy the stack to AWS
```

See `requirements-dev.txt` for development/test dependencies.
