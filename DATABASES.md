# Experian ADO Analytics - Database Schemas & Data Sources

This document describes the database schemas, tables, and data sources you'll work with as an Analytics Analyst at Experian.

---

## 📊 Database Overview

Experian's analytics environment typically consists of 5 integrated databases:

| Database | Purpose | Key Tables | Volume |
|----------|---------|-----------|--------|
| **Credit Bureau DB** | Consumer/business credit reports | `consumer_credit_reports`, `trade_lines`, `inquiries` | 10M+ records |
| **Applications DB** | Loan application workflow | `applications`, `applicants`, `decisions` | 1M+ records/month |
| **Attributes DB** | Calculated bureau attributes | `attribute_definitions`, `attribute_values` | 50M+ records |
| **Benchmark DB** | Industry performance standards | `benchmark_approval_rates`, `benchmark_segments` | 100K+ benchmarks |
| **Monitoring DB** | Quality & performance tracking | `attribute_distributions`, `drift_alerts` | 500K+ daily records |

---

## 1. CREDIT BUREAU DATABASE

### Purpose
Stores consumer and business credit report data pulled from credit bureaus (Equifax, Experian, TransUnion).

### Connection String Examples

**SQL Server:**
```
Server=credit-bureau-server.database.windows.net;
Database=credit_bureau;
User Id=analytics_user;
Password=YourPassword;
```

**PostgreSQL:**
```
postgresql://analytics_user:password@credit-bureau-postgres.c.redshift.amazonaws.com:5432/credit_bureau
```

**MySQL:**
```
mysql://analytics_user:password@credit-bureau-mysql.rds.amazonaws.com:3306/credit_bureau
```

### Schema

#### Table: `consumer_credit_reports`
```sql
CREATE TABLE consumer_credit_reports (
    credit_report_id BIGINT PRIMARY KEY,
    consumer_id BIGINT NOT NULL,
    report_date DATE NOT NULL,
    inquiry_date DATE,
    report_pull_date DATE,
    total_accounts INT,
    total_debt DECIMAL(15,2),
    monthly_payment_amount DECIMAL(10,2),
    credit_score INT,
    bureau_code VARCHAR(5), -- 'EQX' (Equifax), 'TRU' (TransUnion), 'EXP' (Experian)
    report_status VARCHAR(20), -- 'ACTIVE', 'ARCHIVED', 'DISPUTE'
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    INDEX idx_consumer_id (consumer_id),
    INDEX idx_report_date (report_date)
);
```

#### Table: `trade_lines`
Trade lines represent individual credit accounts (credit cards, loans, mortgages).

```sql
CREATE TABLE trade_lines (
    trade_line_id BIGINT PRIMARY KEY,
    credit_report_id BIGINT NOT NULL,
    consumer_id BIGINT NOT NULL,
    account_type VARCHAR(30), -- 'CREDIT_CARD', 'AUTO_LOAN', 'MORTGAGE', 'INSTALLMENT'
    account_status VARCHAR(20), -- 'OPEN', 'CLOSED', 'CHARGED_OFF', 'DELINQUENT'
    open_date DATE,
    close_date DATE,
    credit_limit DECIMAL(12,2),
    current_balance DECIMAL(12,2),
    payment_status VARCHAR(20), -- 'CURRENT', '30_DAYS', '60_DAYS', '90+_DAYS'
    monthly_payment DECIMAL(10,2),
    days_past_due INT,
    last_payment_date DATE,
    creditor_name VARCHAR(100),
    created_at TIMESTAMP,
    FOREIGN KEY (credit_report_id) REFERENCES consumer_credit_reports(credit_report_id),
    INDEX idx_credit_report_id (credit_report_id),
    INDEX idx_consumer_id (consumer_id)
);
```

#### Table: `inquiries`
Credit inquiries made when a consumer applies for credit.

```sql
CREATE TABLE inquiries (
    inquiry_id BIGINT PRIMARY KEY,
    credit_report_id BIGINT NOT NULL,
    consumer_id BIGINT NOT NULL,
    inquiry_date DATE NOT NULL,
    inquiry_type VARCHAR(20), -- 'HARD', 'SOFT'
    inquiry_reason VARCHAR(50), -- 'CREDIT_CARD', 'AUTO_LOAN', 'MORTGAGE', 'EMPLOYMENT'
    creditor_name VARCHAR(100),
    months_ago INT, -- Inquiry age in months
    created_at TIMESTAMP,
    FOREIGN KEY (credit_report_id) REFERENCES consumer_credit_reports(credit_report_id),
    INDEX idx_credit_report_id (credit_report_id),
    INDEX idx_inquiry_date (inquiry_date)
);
```

#### Table: `public_records`
Public record data (bankruptcies, liens, judgments).

```sql
CREATE TABLE public_records (
    public_record_id BIGINT PRIMARY KEY,
    credit_report_id BIGINT NOT NULL,
    consumer_id BIGINT NOT NULL,
    record_type VARCHAR(30), -- 'BANKRUPTCY', 'LIEN', 'JUDGMENT', 'FORECLOSURE'
    filing_date DATE,
    disposition_date DATE,
    amount DECIMAL(12,2),
    status VARCHAR(20), -- 'SATISFIED', 'UNSATISFIED', 'DISMISSED'
    months_ago INT,
    created_at TIMESTAMP,
    FOREIGN KEY (credit_report_id) REFERENCES consumer_credit_reports(credit_report_id),
    INDEX idx_consumer_id (consumer_id)
);
```

---

## 2. APPLICATIONS DATABASE

### Purpose
Tracks loan applications from submission through decision.

### Connection String
```
Server=applications-server.database.windows.net;
Database=applications;
User Id=analytics_user;
Password=YourPassword;
```

### Schema

#### Table: `applicants`
```sql
CREATE TABLE applicants (
    applicant_id BIGINT PRIMARY KEY,
    consumer_id BIGINT NOT NULL UNIQUE,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    date_of_birth DATE,
    email_address VARCHAR(100),
    phone_number VARCHAR(20),
    address_line1 VARCHAR(100),
    address_line2 VARCHAR(100),
    city VARCHAR(50),
    state_code CHAR(2),
    zip_code VARCHAR(10),
    country_code CHAR(2),
    annual_income DECIMAL(12,2),
    employment_status VARCHAR(20), -- 'EMPLOYED', 'SELF_EMPLOYED', 'UNEMPLOYED', 'RETIRED'
    years_employed INT,
    created_at TIMESTAMP,
    INDEX idx_consumer_id (consumer_id)
);
```

#### Table: `applications`
```sql
CREATE TABLE applications (
    application_id BIGINT PRIMARY KEY,
    applicant_id BIGINT NOT NULL,
    consumer_id BIGINT NOT NULL,
    application_date DATE NOT NULL,
    product_type VARCHAR(30), -- 'CREDIT_CARD', 'AUTO_LOAN', 'MORTGAGE', 'PERSONAL_LOAN'
    application_amount DECIMAL(12,2),
    term_months INT,
    requested_credit_limit DECIMAL(12,2),
    channel_code VARCHAR(20), -- 'ONLINE', 'BRANCH', 'PHONE', 'PARTNER'
    purpose VARCHAR(50), -- 'DEBT_CONSOLIDATION', 'HOME_IMPROVEMENT', 'VEHICLE_PURCHASE'
    application_status VARCHAR(20), -- 'SUBMITTED', 'UNDER_REVIEW', 'DECISION_MADE', 'CLOSED'
    decision_date DATE,
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    FOREIGN KEY (applicant_id) REFERENCES applicants(applicant_id),
    INDEX idx_applicant_id (applicant_id),
    INDEX idx_application_date (application_date),
    INDEX idx_consumer_id (consumer_id)
);
```

#### Table: `decisions`
Application decision outcomes.

```sql
CREATE TABLE decisions (
    decision_id BIGINT PRIMARY KEY,
    application_id BIGINT NOT NULL UNIQUE,
    consumer_id BIGINT NOT NULL,
    decision_date DATE NOT NULL,
    decision_outcome VARCHAR(20), -- 'APPROVED', 'DECLINED', 'REFERRED'
    decision_reason VARCHAR(100), -- 'CREDIT_SCORE_LOW', 'INSUFFICIENT_INCOME', 'EXCESSIVE_DEBT', 'POLICY_RULE'
    risk_band VARCHAR(10), -- 'A' (Prime), 'B', 'C', 'D' (Subprime)
    approval_rate DECIMAL(5,2), -- Percentage likelihood of approval
    decision_model_version VARCHAR(20),
    decision_threshold DECIMAL(5,2),
    created_at TIMESTAMP,
    FOREIGN KEY (application_id) REFERENCES applications(application_id),
    INDEX idx_application_id (application_id),
    INDEX idx_decision_date (decision_date),
    INDEX idx_decision_outcome (decision_outcome),
    INDEX idx_consumer_id (consumer_id)
);
```

#### Table: `ado_outcomes`
Application Decision Outcome tracking with follow-up performance.

```sql
CREATE TABLE ado_outcomes (
    ado_id BIGINT PRIMARY KEY,
    application_id BIGINT NOT NULL,
    consumer_id BIGINT NOT NULL,
    decision_id BIGINT NOT NULL,
    decision_outcome VARCHAR(20), -- 'APPROVED', 'DECLINED', 'REFERRED'
    approval_date DATE,
    account_opened_date DATE,
    current_status VARCHAR(20), -- 'ACCOUNT_ACTIVE', 'ACCOUNT_CLOSED', 'DELINQUENT', 'DEFAULTED'
    performance_rating VARCHAR(10), -- 'PERFORMING', 'AT_RISK', 'DELINQUENT', 'DEFAULT'
    months_performance INT,
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    FOREIGN KEY (application_id) REFERENCES applications(application_id),
    FOREIGN KEY (decision_id) REFERENCES decisions(decision_id),
    INDEX idx_application_id (application_id),
    INDEX idx_decision_outcome (decision_outcome),
    INDEX idx_consumer_id (consumer_id)
);
```

---

## 3. ATTRIBUTES DATABASE

### Purpose
Stores bureau-derived and bespoke attributes used in credit decisions.

### Connection String
```
Server=attributes-server.database.windows.net;
Database=attributes;
User Id=analytics_user;
Password=YourPassword;
```

### Schema

#### Table: `attribute_definitions`
Metadata about all available attributes.

```sql
CREATE TABLE attribute_definitions (
    attribute_id INT PRIMARY KEY,
    attribute_name VARCHAR(100) NOT NULL UNIQUE,
    attribute_code VARCHAR(30) NOT NULL UNIQUE, -- e.g., 'DTI', 'CREDIT_UTIL', 'DELINQ_CNT'
    attribute_type VARCHAR(20), -- 'CONTINUOUS', 'CATEGORICAL', 'BINARY'
    source_type VARCHAR(20), -- 'BUREAU', 'ALTERNATIVE', 'CALCULATED'
    description TEXT,
    calculation_logic TEXT, -- SQL or formula for derived attributes
    data_type VARCHAR(20), -- 'DECIMAL', 'INT', 'VARCHAR'
    min_value DECIMAL(10,2),
    max_value DECIMAL(10,2),
    version INT,
    effective_date DATE,
    deprecated_date DATE,
    created_by VARCHAR(50),
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    INDEX idx_attribute_code (attribute_code)
);
```

#### Table: `attribute_values`
Calculated attribute values for each consumer/report.

```sql
CREATE TABLE attribute_values (
    attribute_value_id BIGINT PRIMARY KEY,
    attribute_id INT NOT NULL,
    consumer_id BIGINT NOT NULL,
    application_id BIGINT,
    credit_report_id BIGINT,
    attribute_value DECIMAL(12,4),
    attribute_value_text VARCHAR(50), -- For categorical attributes
    calculation_date DATE,
    is_missing BOOLEAN DEFAULT FALSE,
    imputation_method VARCHAR(20), -- 'NONE', 'MEAN', 'MEDIAN', 'ZERO'
    created_at TIMESTAMP,
    FOREIGN KEY (attribute_id) REFERENCES attribute_definitions(attribute_id),
    INDEX idx_attribute_id (attribute_id),
    INDEX idx_consumer_id (consumer_id),
    INDEX idx_application_id (application_id),
    INDEX idx_calculation_date (calculation_date)
);
```

#### Table: `attribute_history`
Historical tracking of attribute values over time.

```sql
CREATE TABLE attribute_history (
    history_id BIGINT PRIMARY KEY,
    attribute_id INT NOT NULL,
    consumer_id BIGINT NOT NULL,
    history_date DATE,
    attribute_value DECIMAL(12,4),
    period VARCHAR(20), -- 'MONTHLY', 'QUARTERLY', 'ANNUALLY'
    created_at TIMESTAMP,
    FOREIGN KEY (attribute_id) REFERENCES attribute_definitions(attribute_id),
    INDEX idx_attribute_id (attribute_id),
    INDEX idx_consumer_id (consumer_id),
    INDEX idx_history_date (history_date)
);
```

#### Table: `attribute_performance`
Performance metrics of attributes in predicting decisions.

```sql
CREATE TABLE attribute_performance (
    performance_id INT PRIMARY KEY,
    attribute_id INT NOT NULL,
    measurement_date DATE,
    correlation_with_approval DECIMAL(5,4),
    gini_coefficient DECIMAL(5,4), -- Model discriminatory power
    ks_statistic DECIMAL(5,4), -- Kolmogorov-Smirnov test
    approval_rate_quintile_1 DECIMAL(5,2),
    approval_rate_quintile_2 DECIMAL(5,2),
    approval_rate_quintile_3 DECIMAL(5,2),
    approval_rate_quintile_4 DECIMAL(5,2),
    approval_rate_quintile_5 DECIMAL(5,2),
    lift_ratio DECIMAL(5,2),
    sample_size INT,
    created_at TIMESTAMP,
    FOREIGN KEY (attribute_id) REFERENCES attribute_definitions(attribute_id),
    INDEX idx_attribute_id (attribute_id),
    INDEX idx_measurement_date (measurement_date)
);
```

---

## 4. BENCHMARK DATABASE

### Purpose
Industry performance standards for benchmarking client outcomes.

### Connection String
```
Server=benchmark-server.database.windows.net;
Database=benchmarks;
User Id=analytics_user;
Password=YourPassword;
```

### Schema

#### Table: `benchmark_approval_rates`
Industry approval rate benchmarks by product and segment.

```sql
CREATE TABLE benchmark_approval_rates (
    benchmark_id INT PRIMARY KEY,
    benchmark_period DATE, -- End of quarter/month
    product_type VARCHAR(30), -- 'CREDIT_CARD', 'AUTO_LOAN', 'MORTGAGE'
    segment VARCHAR(50), -- 'PRIME', 'SUBPRIME', 'NEAR_PRIME', 'ALL'
    geography VARCHAR(50), -- 'US', 'CA', 'BY_STATE', 'BY_REGION'
    risk_band VARCHAR(10), -- 'A', 'B', 'C', 'D'
    approval_rate DECIMAL(5,2),
    decline_rate DECIMAL(5,2),
    refer_rate DECIMAL(5,2),
    sample_size INT,
    sample_loans INT,
    created_at TIMESTAMP,
    INDEX idx_benchmark_period (benchmark_period),
    INDEX idx_product_type (product_type),
    INDEX idx_risk_band (risk_band)
);
```

#### Table: `benchmark_by_segment`
Benchmarks broken down by consumer attributes.

```sql
CREATE TABLE benchmark_by_segment (
    benchmark_segment_id INT PRIMARY KEY,
    benchmark_period DATE,
    product_type VARCHAR(30),
    segment_dimension VARCHAR(50), -- 'CREDIT_SCORE_RANGE', 'DEBT_TO_INCOME', 'EMPLOYMENT_STATUS', 'GEOGRAPHY'
    segment_value VARCHAR(100), -- e.g., '600-650', '<30%', 'EMPLOYED'
    approval_rate DECIMAL(5,2),
    sample_size INT,
    created_at TIMESTAMP,
    INDEX idx_benchmark_period (benchmark_period),
    INDEX idx_segment_dimension (segment_dimension)
);
```

#### Table: `benchmark_history`
Historical benchmark trends.

```sql
CREATE TABLE benchmark_history (
    history_id INT PRIMARY KEY,
    attribute_name VARCHAR(100),
    measurement_month DATE,
    mean_value DECIMAL(12,4),
    median_value DECIMAL(12,4),
    std_deviation DECIMAL(12,4),
    percentile_10 DECIMAL(12,4),
    percentile_25 DECIMAL(12,4),
    percentile_75 DECIMAL(12,4),
    percentile_90 DECIMAL(12,4),
    sample_size INT,
    created_at TIMESTAMP,
    INDEX idx_measurement_month (measurement_month),
    INDEX idx_attribute_name (attribute_name)
);
```

---

## 5. MONITORING DATABASE

### Purpose
Tracks attribute quality, stability, and performance over time.

### Connection String
```
Server=monitoring-server.database.windows.net;
Database=monitoring;
User Id=analytics_user;
Password=YourPassword;
```

### Schema

#### Table: `attribute_distributions`
Distribution statistics for attributes by period.

```sql
CREATE TABLE attribute_distributions (
    distribution_id INT PRIMARY KEY,
    attribute_id INT NOT NULL,
    measurement_date DATE,
    mean DECIMAL(12,4),
    median DECIMAL(12,4),
    std_dev DECIMAL(12,4),
    min_value DECIMAL(12,4),
    max_value DECIMAL(12,4),
    percentile_5 DECIMAL(12,4),
    percentile_25 DECIMAL(12,4),
    percentile_75 DECIMAL(12,4),
    percentile_95 DECIMAL(12,4),
    missing_count INT,
    missing_pct DECIMAL(5,2),
    distinct_count INT,
    sample_size INT,
    created_at TIMESTAMP,
    INDEX idx_attribute_id (attribute_id),
    INDEX idx_measurement_date (measurement_date)
);
```

#### Table: `drift_alerts`
Detected data quality issues and drifts.

```sql
CREATE TABLE drift_alerts (
    alert_id INT PRIMARY KEY,
    attribute_id INT NOT NULL,
    alert_date DATE,
    alert_type VARCHAR(50), -- 'DISTRIBUTION_SHIFT', 'MISSING_DATA_SURGE', 'OUTLIER_DETECTION', 'CORRELATION_CHANGE'
    severity VARCHAR(10), -- 'LOW', 'MEDIUM', 'HIGH', 'CRITICAL'
    previous_value DECIMAL(12,4),
    current_value DECIMAL(12,4),
    threshold_value DECIMAL(12,4),
    description TEXT,
    is_acknowledged BOOLEAN DEFAULT FALSE,
    resolved_date DATE,
    root_cause VARCHAR(255),
    action_taken VARCHAR(255),
    created_at TIMESTAMP,
    INDEX idx_alert_date (alert_date),
    INDEX idx_severity (severity)
);
```

#### Table: `quality_scores`
Overall data quality score by attribute.

```sql
CREATE TABLE quality_scores (
    score_id INT PRIMARY KEY,
    attribute_id INT NOT NULL,
    measurement_date DATE,
    completeness_score DECIMAL(5,2), -- % non-missing data
    accuracy_score DECIMAL(5,2), -- Validation against known values
    consistency_score DECIMAL(5,2), -- Consistency with related attributes
    timeliness_score DECIMAL(5,2), -- Freshness of data
    overall_quality_score DECIMAL(5,2),
    quality_grade VARCHAR(1), -- 'A', 'B', 'C', 'D', 'F'
    issues_found TEXT,
    created_at TIMESTAMP,
    INDEX idx_attribute_id (attribute_id),
    INDEX idx_measurement_date (measurement_date)
);
```

#### Table: `performance_tracking`
Performance metrics tracking (approval rates, delinquency, etc.).

```sql
CREATE TABLE performance_tracking (
    tracking_id INT PRIMARY KEY,
    tracking_period DATE,
    product_type VARCHAR(30),
    risk_band VARCHAR(10),
    total_applications INT,
    approved_count INT,
    declined_count INT,
    referred_count INT,
    approval_rate DECIMAL(5,2),
    accounts_60_days_delinquent INT,
    accounts_delinquent_pct DECIMAL(5,2),
    accounts_charged_off INT,
    charge_off_rate DECIMAL(5,2),
    average_account_age_months INT,
    created_at TIMESTAMP,
    INDEX idx_tracking_period (tracking_period)
);
```

---

## Common SQL Queries by Use Case

### 1. Extract ADO Outcomes by Attribute
```sql
SELECT 
    ad.attribute_name,
    d.decision_outcome,
    d.risk_band,
    COUNT(*) as count,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (PARTITION BY ad.attribute_name), 2) as pct,
    ROUND(AVG(CASE WHEN d.decision_outcome = 'APPROVED' THEN 1 ELSE 0 END) * 100, 2) as approval_rate
FROM attribute_values av
JOIN attribute_definitions ad ON av.attribute_id = ad.attribute_id
JOIN applications app ON av.application_id = app.application_id
JOIN decisions d ON app.application_id = d.application_id
WHERE ad.attribute_code IN ('CREDIT_UTIL', 'DTI', 'DELINQ_CNT', 'INQ_COUNT')
  AND av.calculation_date >= DATEADD(MONTH, -12, GETDATE())
GROUP BY ad.attribute_name, d.decision_outcome, d.risk_band
ORDER BY ad.attribute_name, d.decision_outcome;
```

### 2. Calculate Attribute Stability (Drift Detection)
```sql
WITH monthly_stats AS (
    SELECT 
        ad.attribute_name,
        DATEADD(MONTH, -1, EOMONTH(ad.measurement_date)) as measurement_month,
        ad.mean,
        ad.std_dev,
        ad.missing_pct,
        LAG(ad.mean) OVER (PARTITION BY ad.attribute_id ORDER BY ad.measurement_date) as prev_mean,
        LAG(ad.std_dev) OVER (PARTITION BY ad.attribute_id ORDER BY ad.measurement_date) as prev_std_dev
    FROM attribute_distributions ad
    WHERE ad.measurement_date >= DATEADD(MONTH, -12, GETDATE())
)
SELECT 
    attribute_name,
    measurement_month,
    mean,
    prev_mean,
    ROUND(ABS((mean - prev_mean) / NULLIF(prev_mean, 0)) * 100, 2) as pct_change,
    CASE 
        WHEN ABS((mean - prev_mean) / NULLIF(prev_mean, 0)) > 0.1 THEN 'DRIFT_ALERT'
        ELSE 'STABLE'
    END as drift_status
FROM monthly_stats
WHERE prev_mean IS NOT NULL
ORDER BY pct_change DESC;
```

### 3. Benchmark Comparison Report
```sql
SELECT 
    app.product_type,
    d.risk_band,
    COUNT(*) as total_decisions,
    ROUND(100.0 * SUM(CASE WHEN d.decision_outcome = 'APPROVED' THEN 1 ELSE 0 END) / COUNT(*), 2) as client_approval_rate,
    bar.approval_rate as industry_benchmark_rate,
    ROUND(
        (100.0 * SUM(CASE WHEN d.decision_outcome = 'APPROVED' THEN 1 ELSE 0 END) / COUNT(*)) 
        - bar.approval_rate, 2
    ) as variance_to_benchmark
FROM applications app
JOIN decisions d ON app.application_id = d.application_id
LEFT JOIN benchmark_approval_rates bar 
    ON app.product_type = bar.product_type
    AND d.risk_band = bar.risk_band
    AND EOMONTH(d.decision_date) = bar.benchmark_period
WHERE d.decision_date >= DATEADD(MONTH, -3, GETDATE())
GROUP BY app.product_type, d.risk_band, bar.approval_rate
ORDER BY app.product_type, d.risk_band;
```

### 4. Attribute Quality Monitoring
```sql
SELECT 
    ad.attribute_name,
    ad.attribute_code,
    MAX(CASE WHEN ad.measurement_date = (SELECT MAX(measurement_date) FROM attribute_distributions) THEN ad.completeness_score END) as latest_completeness,
    MAX(CASE WHEN ad.measurement_date = (SELECT MAX(measurement_date) FROM attribute_distributions) THEN ad.overall_quality_score END) as latest_quality_score,
    MAX(CASE WHEN ad.measurement_date = (SELECT MAX(measurement_date) FROM attribute_distributions) THEN ad.quality_grade END) as latest_grade,
    COUNT(DISTINCT CASE WHEN da.alert_type = 'DISTRIBUTION_SHIFT' THEN da.alert_id END) as drift_alerts_30_days,
    COUNT(DISTINCT CASE WHEN da.severity = 'CRITICAL' THEN da.alert_id END) as critical_alerts_30_days
FROM attribute_definitions ad
LEFT JOIN attribute_distributions ad 
    ON ad.attribute_id = ad.attribute_id
    AND ad.measurement_date >= DATEADD(DAY, -30, GETDATE())
LEFT JOIN drift_alerts da 
    ON ad.attribute_id = da.attribute_id
    AND da.alert_date >= DATEADD(DAY, -30, GETDATE())
GROUP BY ad.attribute_name, ad.attribute_code
ORDER BY latest_quality_score ASC;
```

---

## Sample Data Generation (for testing)

If you need to generate sample data for testing queries:

```sql
-- Generate sample bureau data
INSERT INTO consumer_credit_reports (consumer_id, report_date, total_accounts, total_debt, credit_score, bureau_code)
SELECT 
    ROW_NUMBER() OVER (ORDER BY NEWID()) as consumer_id,
    DATEADD(DAY, -ABS(CHECKSUM(NEWID())) % 365, GETDATE()) as report_date,
    ABS(CHECKSUM(NEWID())) % 10 + 1 as total_accounts,
    ABS(CHECKSUM(NEWID())) % 50000 + 5000 as total_debt,
    ABS(CHECKSUM(NEWID())) % 400 + 300 as credit_score,
    CASE ABS(CHECKSUM(NEWID())) % 3 WHEN 0 THEN 'EQX' WHEN 1 THEN 'TRU' ELSE 'EXP' END as bureau_code
FROM sys.all_columns
LIMIT 100000;
```

---

## Connecting from Python

```python
import pandas as pd
import sqlalchemy as sa

# SQL Server connection
engine = sa.create_engine(
    'mssql+pyodbc://analytics_user:Password@credit-bureau-server.database.windows.net/credit_bureau?driver=ODBC+Driver+17+for+SQL+Server'
)

# Execute query
query = """
SELECT attribute_name, decision_outcome, COUNT(*) as count
FROM attribute_values av
JOIN attribute_definitions ad ON av.attribute_id = ad.attribute_id
JOIN decisions d ON av.application_id = d.application_id
GROUP BY attribute_name, decision_outcome
"""

df = pd.read_sql(query, engine)
print(df)
```

---

## Next Steps

1. **Obtain Credentials:** Request database access from your Experian DBA/Admin
2. **Test Connections:** Use connection strings above to validate access
3. **Execute Sample Queries:** Start with queries in `02-data-engineering/sql/`
4. **Build Python ETL:** Use templates in `02-data-engineering/python/`
5. **Create Power BI Models:** Connect to these databases via Power BI

---

**Last Updated:** September 2026
**Database Version:** 3.0
**Maintenance Schedule:** Weekly schema updates, Daily data refresh
