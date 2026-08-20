## How to Use Data to Reduce Costs and Delays in Construction

A Technical and Strategic Guide with Python and MLOps

Executive Summary

The Challenge: Your construction company generates mountains of data (spreadsheets, photos, ERP) daily, but 88% of companies do not transform it into strategic predictive decisions. The result? Expensive delays and material waste.

The Open-Source Solution: Implement an agile data ecosystem using Python and open-source tools. Start small and scale based on return on investment.

The Impact (Proven ROI):

15-25% Reduction in operational costs and prevention of deviations in the first year.
Initial investment payback in just 1 to 3 months.
Prediction: Detect delay risks 2-4 weeks before they occur.
Your Next Step: Choose a critical process (e.g., daily site measurement) and digitize it this week.

Construction Site Data Flow

For a manager, technical complexity boils down to a continuous cycle of transformation. The following diagram illustrates the data journey, from simple collection to strategic action:

```

graph TD

A[Field Collection: Forms / Apps] --> B(Bronze: Raw Data)

B --> C(dbt / Prefect: Transformation)

C --> D(Silver: Clean / Integrated Data)

D --> E(Gold: Aggregations / ML Features)

E --> F{ML / BI: Predictive Analytics & Dashboards}

F --> G[On-Site Actions: Strategic Decision]

G --> A

```

Detailed View (Bronze-Silver-Gold Architecture):

Bronze: Preservation of original data (auditability).
Silver: Standardized, clean data ready for reporting.
Gold: Data ready for BI (Business Intelligence) and Machine Learning (ML).
Data Architecture: The Technical Foundation {#1-arquitetura-de-dados-a-fundação-técnica}
The data structure must be flexible enough to receive raw data and optimized for analytics. The Bronze-Silver-Gold architecture is the modern standard:



1.1 Data Quality: The Heart of Reliability

Implementing quality checks before any analysis is crucial to avoid "Garbage In, Garbage Out".

```

import pandas as pd

from typing import Dict, List

class DataQualityChecker:

"""Essential data quality validations in the Silver layer"""

def init(self):

self.errors = []

def validate_dataset(self, df: pd.DataFrame, rules: Dict) -> bool:

self.errors = []

# 1. Mandatory fields (Schema Check)

if 'required_columns' in rules:

missing = set(rules['required_columns']) - set(df.columns)

if missing:

self.errors.append(f"Missing columns: {missing}")

# 2. Value ranges (Outlier Check)

if 'value_ranges' in rules:

for col, (min_val, max_val) in rules['value_ranges'].items():

outliers = df[(df[col] < min_val) | (df[col] > max_val)]

if len(outliers) > 0:

self.errors.append(

f"{col}: {len(outliers)} values out of range [{min_val}, {max_val}]"

)

# 3. Null values (Integrity Check)

if 'no_nulls' in rules:

for col in rules['no_nulls']:

null_count = df[col].isnull().sum()

if null_count > 0:

self.errors.append(f"{col}: {null_count} null values")

return len(self.errors) == 0

```


Example usage:

```

rules = {'value_ranges': {'m2_built': (0, 1000)}, 'no_nulls': ['date']}
is_valid = checker.validate_dataset(df_clean, rules)

```

Tech Stack by Maturity Level {#2-stack-tecnológica-por-nível-de-maturidade}
The journey should be incremental. Start with the minimum viable setup and add complexity when the ROI justifies it.

| Level | Primary Objective | Recommended Stack | Estimated Monthly Cost |

|---|---|---|---|

| 1: Foundation | Digitize processes, basic BI. | Python, Streamlit (Dashboard), PostgreSQL (Data). | $10 - $40 |



| 2: Intelligence | ETL Automation, Simple Predictive Analytics. | Prefect (Orchestration), DuckDB (Fast Analytics), AWS S3 (Data Lake). | $100 - $300 |

| 3: Prediction | Machine Learning in Production (MLOps), Computer Vision. | FastAPI (ML API), MLflow (Tracking), YOLOv8 (Computer Vision). | $600 - $3,000 |

Level 2: Orchestration and Integration (ETL)

Automation is essential. We use Prefect for scheduling and DuckDB for fast, efficient analytics on Parquet files in the Data Lake (S3).

```

from prefect import flow, task

from datetime import timedelta

import pandas as pd

@task(retries=3, retry_delay_seconds=30)

def extract_from_erp() -> pd.DataFrame:

"""Extracts data from ERP, handling temporary connection failures."""

```


# Simulation - in production, connects to SQL Server/SAP B1

# ... extraction code ...

return pd.DataFrame(...)

@task

def transform_data(df: pd.DataFrame) -> pd.DataFrame:

"""Applies Feature Engineering and Data Quality checks."""

# Creates productivity features (cost/sqm, accumulated sqm, etc.)

df['cost_sqm'] = df['daily_cost'] / df['built_sqm']

# ... other transformations for the Silver layer ...

return df

Strategic and Cultural Implementation {#3-implementação-estratégica-e-cultural}
Technology is only half the battle. Adoption and data culture are crucial for success.

3.1 Overcoming the Cultural Challenge

Focus on Value: Prioritize digitizing a critical pain point process (e.g., manual measurement collection) to deliver value quickly.
Training (Data Literacy): Field teams need to understand how the data they input directly impacts their day-to-day work. Use Streamlit (Level 1) to give them immediate visualization of their data.
Adoption: Adopt a "source collection" approach using simple forms (Google Forms/Microsoft Forms), eliminating spreadsheet rework.
3.2 Dealing with Legacy Systems

Data integration (ELT - Extract, Load, Transform) is the key.

Extraction (E): Use SQLAlchemy (Python) to interact with legacy databases (SQL Server, Oracle) or ERP APIs. For decentralized spreadsheets, CSV/Excel reading scripts (pandas) act as the Bronze extractor.
Transformation (T): In Level 2, consider adopting dbt (data build tool). It allows data analysts (not just engineers) to define transformation rules (cleaning, data joining) using SQL alone, managing complex logic in a modular and testable way.
Applied Machine Learning: Prediction and Vision {#4-4-machine-learning-aplicado-predição-e-visão}
INSERT FIGURE 3

In Level 3, the focus shifts to predictive systems that generate actionable recommendations.

4.1 Real Case: Delay Risk Prediction

The model must be predictive (forward-looking) and explainable.

Critical Features:



Relative Productivity: Current productivity vs. historical average for that specific activity.
Variability (Risk): Standard Deviation (STD) of productivity over the last 7 days.
Exogenous Factors: Accumulated rainfall, number of non-conformities (rework indicator).
Training the risk model (GradientBoostingClassifier)
... (training and predict code according to original article) ...
def predict(self, X: pd.DataFrame) -> dict:

"""Prediction with Risk Level and Actionable Explanation."""

# Assuming scaler and model are loaded

# ... (prediction code omitted for brevity) ...

```

risk_level = 'HIGH' # ... (calculated) ...

actions = []

if risk_level == 'HIGH':

actions.append(" URGENT: Review materials and workforce immediately.")

return {

'delay_probability': round(prob * 100, 1),

'risk': risk_level,

'recommended_actions': actions

}

```

4.2 Computer Vision: Safety and Productivity

Using YOLOv8 (Ultralytics) to detect Personal Protective Equipment (PPE) or physical progress delivers one of the fastest ML ROIs.

Savings: Reduces accidents (massive indirect savings) and fines.
Implementation: Fine-tuning a base model with 500-1,000 images from your construction site is sufficient for a high-accuracy MVP.
Real Costs and Detailed ROI Analysis {#5-custos-reais-e-análise-de-roi-detalhada}
Practical Results and Success Stories

• Fictional Case 1: Alpha Engineering implemented the Daily Dashboard (Level 1). By monitoring the cost/sqm KPI in real time, they identified an anomaly in a foundation process.

Early correction saved $40,000 in additional expenses.

• Fictional Case 2: Beta Construction applied the Predictive Model (Level 2). The system alerted a delay risk 3 weeks before it was scheduled to impact the timeline.

With the extra lead time, management adjusted material and labor logistics, reducing the final project delay from 30 to 5 days—an 18% reduction in overall delay time.

Cost Breakdown and Personnel by Stage

| Level | Key Personnel | Initial Investment () | Recurrent Monthly Cost () | Estimated Annual ROI | Payback (Months) |

|---|---|---|---|---|---|

| 1: Foundation | Site Engineer (10% time), Python Consultant (40h) | $3,000 | $10 - $40 | $20,000 - $60,000 | 1 - 3 |

| 2: Intelligence | Junior Data Analyst | $6,000 | $1,300 | $50,000 - $160,000 | 3 - 6 |

| 3: Prediction | Senior Data Scientist, ML Engineer | $16,000 | $6,000 | $100,000 - $400,000+ | 6 - 12 |

ROI Calculator

A conservative estimate is that data-driven efficiency represents 2% to 10% of annual revenue.

Example usage
calc = ROICalculator(annual_revenue=10_000_000, num_projects=10)

print(" ROI Analysis for $10 Million Annual Revenue\n")

for level in [1, 2, 3]:

result = calc.calculate_roi(level)

print(f"Level {level}:")

print(f" Initial investment: ${result['initial_investment']:,.0f}")

print(f" Year 1 cost: ${result['year_1_cost']:,.0f}")

print(f" Estimated gain (Year 1): ${result['estimated_gain_year_1']:,.0f}")

print(f" ROI (Year 1): {result['roi_year_1']}%")

print(f" Payback: {result['payback_months']} months\n")

Your 30-Day Plan: Consistent Execution {#6-seu-plano-de-30-dias-a-execução-consistente}
Week 1: Technical Setup and Digitization

Days 1-5: Set up Python environment, Database (PostgreSQL), and implement the Simple Form for collection.
Week 2: Dashboard and Feedback

Days 6-14: Implement Data Quality checks, create the Streamlit Dashboard (v1.0), and present it to the field team.
Week 3: Automation and Data Lake

Days 15-21: Connect data extraction from Forms/ERP and build the Bronze → Silver transformation pipeline.
Week 4: First Predictive Analysis

Days 22-30: Create Simple Features (Average Productivity), train a Simple Prediction Model, and integrate Risk Alerts into the Dashboard.
Conclusion — Data as the Operational Foundation

Construction generates a significant volume of information daily, but much of this potential remains underutilized due to a lack of processes, standardization, and technology integration.

The goal of this guide was to demonstrate that adopting a simple, incremental, and operations-oriented data architecture can turn scattered records into reliable metrics, functional predictive models, and control routines that reduce variability and inefficiencies.

The results presented do not depend on complex solutions or massive investments—they depend on collection discipline, modeling consistency, and continuous automation.

Companies that successfully build this cycle can anticipate problems, increase measurement accuracy, and improve resource allocation based on evidence rather than subjective perceptions.

Ultimately, the strategy is not about "using AI," but about ensuring that data is organized, trustworthy, and accessible.

Only then is it possible to apply reliable analytics and evolve toward predictive models that genuinely support field decisions.

Construction will always face external variables—weather, labor, suppliers, design changes—but internal variability can be significantly reduced.

Well-instrumented processes and structured data make the construction site more predictable, allowing engineering teams to work with higher precision and less uncertainty.

Therefore, the next natural step for any company is simple: establish a consistent database and gradually evolve from it.

This is the technical foundation supporting every future automation, analytics, and modeling initiative.






