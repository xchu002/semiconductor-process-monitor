# Semiconductor Process Monitor & Yield Investigation

A Python-based semiconductor process engineering project exploring statistical process control (SPC), process capability, tool-to-tool variation, yield analysis, and process excursion investigation using synthetic wafer manufacturing data.

## Project Objective

Semiconductor manufacturing requires processes to remain stable across wafers, lots, and equipment. Small process shifts or equipment excursions can lead to defects and yield loss.

This project simulates a wafer manufacturing process and applies statistical methods to answer questions such as:

- Is the process capable of meeting specification limits?
- Is the process statistically stable?
- Are different tools producing systematically different results?
- Which production lots have abnormal defect or yield performance?
- Can the observed excursion be explained by recorded process parameters?

The dataset is **synthetically generated for educational and portfolio purposes** and does not represent data from an actual semiconductor fab.

---

## Dataset

The simulated dataset contains **500 wafers across 20 production lots and 3 processing tools**.

Recorded variables include:

| Variable | Description |
|---|---|
| `wafer_id` | Unique wafer identifier |
| `lot_id` | Production lot |
| `tool_id` | Processing tool (A, B, or C) |
| `temperature_C` | Process temperature |
| `pressure_mTorr` | Chamber pressure |
| `deposition_time_s` | Deposition time |
| `film_thickness_nm` | Resulting film thickness |
| `defect_count` | Number of simulated defects |
| `yield_pct` | Simulated wafer yield |

The synthetic process includes normal random variation, tool-to-tool offsets, process-variable effects, and a deliberately introduced abnormal production lot for investigation.

---

## Analysis

### 1. Process Capability

Film thickness was evaluated against engineering specification limits using:

- Cp
- Cpu / Cpl
- Cpk

This demonstrates the distinction between **process spread** and **process centering**.

### 2. Statistical Process Control

A baseline process was used to calculate:

- Process mean
- Standard deviation
- Upper Control Limit (UCL)
- Lower Control Limit (LCL)

The monitoring logic detects:

- Measurements beyond 3-sigma control limits
- Sustained sequences above or below the process mean
- Automated SPC alarm reasons

This also demonstrates the distinction between **control limits**, which describe expected process behaviour, and **specification limits**, which define engineering acceptance requirements.

### 3. Tool-to-Tool Comparison

Processing tools were compared using:

- Mean film thickness
- Process variation
- Cpk
- Box plots
- Welch's independent t-test

The synthetic data showed a statistically detectable thickness offset between processing tools despite relatively similar within-tool variation.

### 4. Lot-Level Yield Investigation

Production lots were ranked by:

- Mean defect count
- Mean yield
- Mean film thickness

This identified **Lot L012** as the major process excursion.

| Metric | L012 | Other Lots |
|---|---:|---:|
| Mean defect count | 11.92 | 3.65 |
| Mean yield | 92.78% | 96.45% |
| Mean film thickness | 100.098 nm | 100.094 nm |

L012 therefore exhibited a large increase in defects and substantial yield loss without an obvious shift in average film thickness.

### 5. Root-Cause Investigation

L012 was compared against the rest of production using Welch's t-tests.

| Process Variable | p-value |
|---|---:|
| Temperature | 0.467 |
| Pressure | 0.817 |
| Deposition time | 0.828 |
| Film thickness | 0.969 |

At a significance level of 0.05, no statistically significant mean shift was detected in these recorded process variables.

The tool distribution within L012 was also broadly similar to overall production, providing no obvious evidence that the excursion resulted simply from an unusual concentration of wafers on one tool.

Meanwhile, defect count showed a strong negative association with yield:

**Pearson correlation: -0.902**

The investigation therefore localizes the problem to a defect excursion affecting L012, while the available process variables do not identify its underlying physical cause.

In a real manufacturing environment, the next investigation would require additional information such as equipment sensor traces, maintenance history, chamber condition, particle monitoring, wafer maps, metrology data, material batches, or detailed process logs.

---

## Correlation Analysis

Across all wafers, film thickness showed the following correlations:

| Variable | Correlation with Thickness |
|---|---:|
| Temperature | 0.615 |
| Deposition time | 0.360 |
| Pressure | 0.151 |
| Yield | -0.144 |
| Defect count | 0.065 |

Temperature showed the strongest linear association with film thickness.

The project also demonstrates an important limitation of Pearson correlation: a variable may have a meaningful **nonlinear relationship** while having little linear correlation.

For example, if deviations both above and below a target temperature increase defects, raw temperature may have near-zero correlation with defects even though distance from the target temperature is important.

---

## Key Findings

1. Statistical control limits and engineering specification limits answer different questions and should not be used interchangeably.
2. Tool-to-tool offsets can exist even when individual tools show similar process variation.
3. Lot-level aggregation can reveal excursions that are difficult to see from individual wafer measurements.
4. L012 showed approximately three times the normal defect count and a substantial yield reduction.
5. The recorded temperature, pressure, deposition time, thickness, and tool distribution did not provide an obvious explanation for the L012 excursion.
6. Strong correlation can help prioritize an investigation, but correlation alone does not establish root cause.
7. Near-zero Pearson correlation does not rule out nonlinear process relationships.

---

## Repository Structure

```text
semiconductor-process-monitor/
│
├── data/
│   └── wafer_process_data.csv
│
├── figures/
│   ├── defects_by_lot.png
│   └── yield_by_lot.png
│
├── 01_process_capability.py
├── 02_spc_analysis.py
├── 03_tool_comparison.py
├── 04_process_investigation.py
├── generate_data.py
├── requirements.txt
├── .gitignore
└── README.md
```

## Technologies

- Python
- pandas
- NumPy
- SciPy
- Matplotlib

## Running the Project

Install the required packages:

```bash
pip install -r requirements.txt
```

Generate the synthetic manufacturing dataset:

```bash
python generate_data.py
```

The individual analyses can then be run sequentially:

```bash
python 01_process_capability.py
python 02_spc_analysis.py
python 03_tool_comparison.py
python 04_process_investigation.py
```

## Limitations

This project is an educational simulation rather than an analysis of real fab data.

The process relationships, coefficients, specification limits, equipment effects, and abnormal conditions were deliberately constructed to provide a realistic environment for practicing semiconductor process-data analysis. They should not be interpreted as representative parameters for a specific deposition process or manufacturing facility.

Statistical significance is also not equivalent to physical or engineering significance. Real process investigations would require domain knowledge, equipment history, additional metrology, and confirmation experiments before assigning root cause.