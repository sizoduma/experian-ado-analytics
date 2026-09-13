# Experian ADO Analytics - Comprehensive Learning Repository

**Analytics Analyst Learning Path for Application Decision Outcome (ADO) Analysis, Attribute Development, and Deneb/Vega-Lite Visualization**

---

## 📋 Table of Contents
1. [Repository Overview](#repository-overview)
2. [Learning Phases](#learning-phases)
3. [Folder Structure](#folder-structure)
4. [Getting Started](#getting-started)
5. [Data Sources & SQL Queries](#data-sources--sql-queries)
6. [Key Technologies](#key-technologies)
7. [Project Milestones](#project-milestones)

---

## Repository Overview

This repository is designed to support your journey as an **Analytics Analyst** at Experian, covering:

- ✅ **Credit Industry Fundamentals** — ADO framework, bureau data, attribute lifecycle
- ✅ **Data Engineering** — SQL extraction, Python transformation, data quality validation
- ✅ **Analytics & Visualization** — Power BI dashboards, Vega-Lite specifications, Deneb custom visuals
- ✅ **Real-World Projects** — ADO analysis, attribute monitoring, benchmark reporting

**Target Duration:** 8 weeks (self-paced)

---

## Learning Phases

### Phase 1-2: Credit Industry & Analytics Fundamentals (Weeks 1-3)
**Goal:** Understand ADO framework and credit lifecycle

#### Key Concepts
- **Application Decision Outcome (ADO):** Approved, Declined, Referred decisions
- **Bureau Data:** Credit reports from Equifax, Experian, TransUnion
- **Attributes:** Calculated fields predicting credit risk (e.g., debt-to-income ratio, payment history)
- **Attribute Monitoring:** Track stability, drift, and predictive power over time
- **Benchmarking:** Compare client performance vs. industry standards

#### Learning Outcomes
- [ ] Understand ADO framework and decision lifecycle
- [ ] Explain bureau data types (consumer, business, alternative)
- [ ] Define attribute development process
- [ ] Document attribute quality metrics

**Resources:**
- Study folder: `01-credit-analytics-fundamentals/`
- SQL queries: `02-data-engineering/sql/01_bureau_data_queries.sql`

---

### Phase 3-4: Power BI & Data Visualization Foundations (Weeks 3-4)
**Goal:** Master Power BI dashboarding for analytics

#### Key Concepts
- **Data Modeling:** Star schema for ADO analytics
- **DAX Expressions:** KPI calculations, conditional logic
- **Report Design:** Best practices for executive dashboards
- **Performance Optimization:** Query folding, aggregations

#### Learning Outcomes
- [ ] Design Power BI data models for ADO data
- [ ] Create DAX measures for approval rates, risk bands
- [ ] Build ADO outcome analysis dashboards
- [ ] Optimize report performance

**Resources:**
- Study folder: `03-powerbi-fundamentals/`
- Templates: `04-power-bi-templates/`

---

### Phase 5: Vega-Lite & Deneb Mastery (Weeks 4-6)
**Goal:** Create advanced custom visualizations

#### Key Concepts
- **Declarative Visualization:** Vega-Lite JSON specifications
- **Vega Grammar:** Marks, encoding, transforms, layers
- **Deneb Integration:** Embedding Vega-Lite in Power BI
- **Interactivity:** Cross-filtering, drill-through, parameter binding

#### Learning Outcomes
- [ ] Write Vega-Lite specifications for 10+ chart types
- [ ] Create Deneb custom visuals in Power BI
- [ ] Build interactive ADO analysis dashboards
- [ ] Template reusable visualizations

**Resources:**
- Study folder: `05-vega-lite-deneb/`
- Templates: `06-deneb-templates/`
- Examples: `07-visualization-examples/`

---

### Phase 6: End-to-End Project Delivery (Weeks 6-8)
**Goal:** Execute complete analytics projects

#### Project Types
1. **Custom Attribute Development**
   - Extract bureau data → Calculate attribute → Validate → Visualize
   
2. **ADO Benchmark Report**
   - Compare client outcomes vs. industry standards by segment
   
3. **Attribute Monitoring Dashboard**
   - Track distributions, alert on drift, monitor quality
   
4. **Interactive ADO Analysis Tool**
   - Multi-dimensional filtering, drill-through, drill-down

**Resources:**
- Study folder: `08-projects/`
- SQL templates: `02-data-engineering/sql/`
- Python notebooks: `02-data-engineering/python/`

---

## Folder Structure

```
experian-ado-analytics/
├── README.md (this file)
├── LEARNING_GUIDE.md (comprehensive curriculum)
├── DATABASES.md (data sources & connection strings)
│
├── 01-credit-analytics-fundamentals/
│   ├── README.md
│   ├── ADO_Framework.md
│   ├── Bureau_Data_Types.md
│   ├── Attribute_Development_Lifecycle.md
│   └── Quality_Metrics.md
│
├── 02-data-engineering/
│   ├── README.md
│   ├── sql/
│   │   ├── 01_bureau_data_queries.sql
│   │   ├── 02_attribute_calculation.sql
│   │   ├── 03_ado_analysis.sql
│   │   ├── 04_benchmark_comparison.sql
│   │   └── 05_attribute_monitoring.sql
│   ├── python/
│   │   ├── 01_data_extraction.py
│   │   ├── 02_data_cleaning.py
│   │   ├── 03_attribute_validation.py
│   │   ├── 04_quality_metrics.py
│   │   └── requirements.txt
│   └── notebooks/
│       ├── 01_bureau_data_exploration.ipynb
│       ├── 02_attribute_development.ipynb
│       └── 03_ado_analysis.ipynb
│
├── 03-powerbi-fundamentals/
│   ├── README.md
│   ├── Data_Modeling_Guide.md
│   ├── DAX_Expressions.md
│   └── Report_Design_Best_Practices.md
│
├── 04-power-bi-templates/
│   ├── README.md
│   ├── ADO_Summary_Dashboard.pbix
│   ├── Attribute_Performance.pbix
│   └── Benchmark_Analysis.pbix
│
├── 05-vega-lite-deneb/
│   ├── README.md
│   ├── Vega_Lite_Fundamentals.md
│   ├── Deneb_Integration_Guide.md
│   └── interactive-examples.html
│
├── 06-deneb-templates/
│   ├── README.md
│   ├── 01_ado_approval_rate.json
│   ├── 02_attribute_performance_lollipop.json
│   ├── 03_approval_rate_heatmap.json
│   ├── 04_trend_forecast.json
│   ├── 05_attribute_segmentation.json
│   └── template_registry.json
│
├── 07-visualization-examples/
│   ├── README.md
│   ├── bar_charts.json
│   ├── line_charts.json
│   ├── scatter_plots.json
│   ├── heatmaps.json
│   └── multi_layer_dashboards.json
│
├── 08-projects/
│   ├── README.md
│   ├── project-01-custom-attribute/
│   │   ├── README.md
│   │   ├── business_requirements.md
│   │   ├── extract_queries.sql
│   │   ├── transform_script.py
│   │   ├── validation_metrics.py
│   │   └── deneb_visualization.json
│   ├── project-02-ado-benchmark/
│   │   ├── README.md
│   │   ├── benchmark_queries.sql
│   │   ├── analysis_notebook.ipynb
│   │   └── dashboard_spec.json
│   ├── project-03-attribute-monitoring/
│   │   ├── README.md
│   │   ├── monitoring_queries.sql
│   │   ├── alert_rules.py
│   │   └── monitoring_dashboard.json
│   └── project-04-interactive-analysis/
│       ├── README.md
│       ├── data_preparation.sql
│       ├── interactive_spec.json
│       └── power_bi_setup.md
│
└── docs/
    ├── glossary.md
    ├── best_practices.md
    ├── troubleshooting.md
    └── references.md
```

---

## Getting Started

### Prerequisites
- SQL Server / PostgreSQL / MySQL (see DATABASES.md)
- Python 3.9+ with pandas, numpy, sqlalchemy
- Power BI Desktop
- Git (for version control)
- Text editor (VS Code recommended)

### Quick Start (Week 1)

**Day 1-2:** Read fundamentals
```bash
cd 01-credit-analytics-fundamentals/
# Read ADO_Framework.md, Bureau_Data_Types.md
```

**Day 3-4:** Extract sample data
```bash
# Execute SQL from 02-data-engineering/sql/01_bureau_data_queries.sql
# Save results to CSV
```

**Day 5:** Explore data
```bash
# Run Python notebook: 02-data-engineering/notebooks/01_bureau_data_exploration.ipynb
```

---

## Data Sources & SQL Queries

### Available Databases

1. **Credit Bureau Database** — Consumer credit report data
   - Tables: `consumer_credit_reports`, `trade_lines`, `inquiries`, `public_records`
   
2. **Applications Database** — Loan application data
   - Tables: `applications`, `applicants`, `decisions`, `ado_outcomes`
   
3. **Attributes Database** — Calculated bureau attributes
   - Tables: `attribute_definitions`, `attribute_values`, `attribute_history`, `attribute_performance`
   
4. **Benchmark Database** — Industry standards
   - Tables: `benchmark_approval_rates`, `benchmark_by_segment`, `benchmark_history`
   
5. **Monitoring Database** — Quality & stability tracking
   - Tables: `attribute_distributions`, `drift_alerts`, `quality_scores`, `performance_tracking`

### Sample Queries

**Extract ADO Outcomes by Attribute:**
```sql
SELECT 
    a.attribute_name,
    d.decision_outcome,
    COUNT(*) as count,
    ROUND(100.0 * COUNT(*) / SUM(COUNT(*)) OVER (PARTITION BY a.attribute_name), 2) as pct
FROM attributes a
JOIN applications app ON a.application_id = app.application_id
JOIN decisions d ON app.application_id = d.application_id
GROUP BY a.attribute_name, d.decision_outcome
ORDER BY a.attribute_name, d.decision_outcome;
```

**See more:** `02-data-engineering/sql/` folder

---

## Key Technologies

| Technology | Purpose | Level |
|-----------|---------|-------|
| **SQL** | Data extraction, transformation | Intermediate+ |
| **Python** | Data cleaning, validation, analysis | Intermediate |
| **Power BI** | Dashboard development, reporting | Intermediate+ |
| **Vega-Lite** | Declarative visualization JSON | Beginner |
| **Deneb** | Power BI custom visuals | Intermediate |
| **GitHub** | Version control, collaboration | Beginner |

---

## Project Milestones

| Week | Phase | Milestone | Deliverable |
|------|-------|-----------|-------------|
| 1-2 | 1-2 | Domain Mastery | ADO framework document |
| 2-3 | 1-2 | SQL Proficiency | Bureau data extraction script |
| 3-4 | 3-4 | Power BI Foundations | ADO summary dashboard |
| 4-5 | 5 | Vega-Lite Mastery | 5 Vega-Lite specifications |
| 5-6 | 5 | Deneb Integration | 3 custom Deneb visuals |
| 6-8 | 6 | Project Execution | Complete ADO monitoring dashboard |

---

## External Resources

### GitHub Repositories
- **Deneb-viz/deneb:** https://github.com/deneb-viz/deneb
- **Chart Examples:** https://github.com/nsdevaraj/chart-playground-palette
- **Power BI Curriculum:** https://github.com/leyonlamar/cortex-learning

### Documentation
- Vega-Lite: https://vega.github.io/vega-lite/
- Deneb Docs: https://deneb-viz.github.io/
- Power BI: https://learn.microsoft.com/power-bi/

### Interactive Tools
- Vega Editor: https://vega.github.io/editor/
- Observable: https://observablehq.com/

---

## How to Use This Repository

1. **Clone the repository:**
   ```bash
   git clone https://github.com/sizoduma/experian-ado-analytics.git
   cd experian-ado-analytics
   ```

2. **Follow the learning phases in order**
   - Start with `01-credit-analytics-fundamentals/`
   - Progress through each phase sequentially

3. **Execute SQL queries** from `02-data-engineering/sql/`
   - Customize for your database connection
   - Save results for Python analysis

4. **Run Python notebooks**
   - Install dependencies: `pip install -r 02-data-engineering/python/requirements.txt`
   - Execute: `jupyter notebook 02-data-engineering/notebooks/`

5. **Build Power BI dashboards**
   - Use templates from `04-power-bi-templates/`
   - Connect to your database via SQL queries

6. **Create Deneb visualizations**
   - Reference templates from `06-deneb-templates/`
   - Adapt to your data structure

7. **Complete projects**
   - Follow guided projects in `08-projects/`
   - Document your approach and results

---

## Contributing & Updates

- 📝 Update notes as you progress
- 🔄 Modify queries for your database schema
- 💾 Commit learnings to GitHub
- 🤝 Share templates and best practices

---

## Support & Troubleshooting

- **SQL Issues:** See `docs/troubleshooting.md`
- **Python Errors:** Check `02-data-engineering/python/requirements.txt`
- **Power BI Connections:** Review `03-powerbi-fundamentals/`
- **Deneb Specs:** Browse `06-deneb-templates/` for examples

---

## License

MIT License - Feel free to use, modify, and share this repository.

---

**Last Updated:** September 2026
**Status:** Active Learning Repository
**Version:** 1.0

---

## Next Steps

👉 **Start here:** Read `LEARNING_GUIDE.md` for detailed curriculum

👉 **Then explore:** `01-credit-analytics-fundamentals/` for domain knowledge

👉 **Connect database:** Follow `DATABASES.md` for setup instructions

Good luck with your Analytics Analyst journey at Experian! 🚀
