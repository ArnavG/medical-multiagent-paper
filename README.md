# SAS_project — Medical Multi-Agent System for Diagnosis → Procedures → Billing

This repository corresponds to our paper on a modular, agentic pipeline that translates patient-presenting symptoms into (1) ranked differential diagnoses, (2) recommended procedures with standardized medical codes, and (3) patient-facing, line-item cost estimates.

> **Code availability:** The full implementation is **available upon request**.

---

## Overview

We present a **multi-agent system** designed to model how uncertainty and information loss propagate across a realistic healthcare workflow:

1. **Diagnosis Agent**  
   Produces a ranked differential diagnosis from free-text symptoms and setting context (e.g., ER vs. outpatient), and aligns outputs to standardized **ICD-10-CM** codes.

2. **Procedure Agent (RAG-based)**  
   Given symptoms + candidate diagnoses, proposes clinically plausible procedures and maps them to **ICD-10-PCS** and **HCPCS/CPT** codes using **retrieval-augmented generation (RAG)** over structured code databases (rather than relying on free-form generation).

3. **Billing Agent**  
   Computes **Medicare-style** cost estimates using structured fee schedules (e.g., PFS/CLFS) and common patient responsibility rules (deductible remaining, coinsurance), returning transparent, line-item outputs.

The system is evaluated on clinically grounded benchmarks for coding accuracy and robustness, including tests with “decoy” codes to measure susceptibility to retrieval errors and model overconfidence.

---

## Example (CLI-style)

Below is an example of how the pipeline is invoked (illustrative):

- Input: symptoms, patient demographics, and care setting  
- Config: model choices per agent  
- Data: ICD / HCPCS reference files + fee schedules  
- Output: full structured JSON trace + final summary

```
python3 pipeline.py
--symptoms "Chest pain, shortness of breath"
--age 64
--sex M
--setting ER
--diagnosis_model openai/gpt-oss-120b
--procedure_model gpt-oss-120b-131072
--billing_model openai/gpt-oss-20b
--icd_parquet procedure_agent/data/icd10pcs_codes.parquet
--hcpcs_csv procedure_agent/data/hcpcs_codes_2025.csv
--pfs_path billing_agent/data/PF_data.parquet
--clfs_path billing_agent/data/CLFS2025.csv
--deductible_remaining 200.0
--output full_pipeline_output.json
```


The final printed output summarizes:
- Patient input
- Top diagnosis candidates (+ ICD-10-CM codes, confidence)
- Procedures (+ HCPCS/CPT + ICD-10-PCS evidence) with line-item costs and OOP breakdown

---

## Key Contributions

- **Agentic decomposition** of a realistic clinical-to-financial workflow (diagnosis → procedures → billing) to improve reliability and traceability.
- A **RAG-based Procedure Agent** that grounds code predictions in structured medical code corpora (ICD-10-PCS, HCPCS/CPT), reducing hallucinations and enabling robust evaluation.
- A pricing module that generates **patient-facing, line-item cost estimates** with explicit deductible/coinsurance reasoning and transparent code provenance.

---

## Repository Structure (Conceptual)

While the full implementation is not included, the system is organized around three agent modules:

- `diagnosis_agent/` — symptom → differential diagnosis + ICD-10-CM alignment  
- `procedure_agent/` — diagnosis/context → procedure intents + code mapping (RAG)  
- `billing_agent/`` — codes → fee schedule lookup + patient responsibility logic  
- `pipeline.py` — orchestrates the agents and produces structured JSON outputs

---

## Paper

If you use or cite this work, please cite the paper (add your bibtex / venue here).

---

## Contact

For code access, questions, or collaboration requests, please reach out to the authors.
