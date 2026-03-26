# 📊 Data Pipeline – README

## 🚀 Overview

This project implements an end-to-end data pipeline using a **Medallion Architecture (Bronze → Silver → Gold)** to ingest, transform, and model API data into an analytics-ready **star schema**.

The pipeline is built using:

* Docker (for environment setup)
* PySpark (for transformations)
* SQL (for modeling)

---

## 🧠 Technical Decisions & Reasoning

### 🔹 Medallion Architecture

Adopted Bronze → Silver → Gold because:

* Clear separation of concerns (raw → clean → business-ready)
* Easier debugging and traceability
* Scalable for production use

---

### 🔹 Silver Layer

* Removed duplicates
* Casted columns to correct data types
* Applied data validation rules
* Added `_is_valid` flag for tracking bad records
* Implemented **upsert logic using window functions**

**Decision:**
Instead of dropping invalid records, I retained them with a flag to allow auditability.

---

### 🔹 Gold Layer

* Built **star schema (fact + dimension tables)**
* Generated **deterministic surrogate keys using SHA2**
* Maintained **referential integrity**

**Trade-off:**
SHA2 adds computation overhead but ensures consistency across runs.

---

## 🔄 API Handling & Resilience Strategy

### Issues Faced

* Infinite loop due to missing `page += 1`
* Typo in loop condition (`while True`)
* API pagination logic errors

### Fixes Applied

* Corrected pagination logic
* Fixed loop condition
* Ensured pipeline is **idempotent (safe to rerun)**

### Future Improvements

* Add retry mechanism with exponential backoff
* Add timeout handling
* Log failed API calls for reprocessing

---

## 🛠️ Development Challenges & Fixes

### 🐳 Docker Issues

* Docker took hours to start initially (system constraints)
* Container health dependency caused startup failure

**Fix:**

```yaml
depends_on:
  - mock-api
```

👉 Removed strict health check dependency.

---

### 💥 Docker Corruption

* `docker-desktop-data` got corrupted

**Fix:**

* Fully uninstalled Docker
* Reinstalled and reset environment

---

### 🔧 Git Setup Issues

* Git not installed initially

**Fix:**

* Installed Git and configured PATH
* Resolved conflict:

  > Pulled upstream changes and merged histories before pushing local commits

---

### 🐞 Coding Issues

* Missing `page += 1` → infinite loop
* Typo in loop condition

---

## ⚖️ Assumptions & Trade-offs

### Assumptions

* API structure remains consistent
* Source file naming pattern is stable
* Minimal late-arriving data

### Trade-offs

* Used **window-based upsert** instead of CDC (simpler but less scalable)
* Limited monitoring/logging in current setup
* Relaxed Docker health checks for local stability

---

## ☁️ Production Deployment (Azure / Microsoft Fabric)

### ⏱️ Scheduling

* Use **Azure Data Factory / Fabric pipelines**
* Implement incremental loads

### 📊 Monitoring

* Azure Monitor / Log Analytics
* Alerts for failures and SLA breaches

### 🔁 CI/CD

* GitHub Actions / Azure DevOps
* Automated testing & deployments

### 🔐 Security

* Azure Key Vault for secrets
* Managed identities

### 💰 Cost Optimization

* Auto-scaling clusters
* Partitioned Parquet storage
* Efficient compute usage

---

## 📚 Key Learnings

* Environment setup (Docker, Git) can be a major blocker
* Small logic bugs can break pipelines (pagination, loops)
* Idempotent design is critical
* Layered architecture simplifies scaling and debugging

---

## ✅ Conclusion

This pipeline:

* Reliably ingests API data
* Cleans and validates datasets
* Produces analytics-ready star schema

Production readiness would require enhancements in:

* Monitoring
* Automation
* Scalability

---
