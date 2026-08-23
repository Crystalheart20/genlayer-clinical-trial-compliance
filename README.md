# Clinical Trial Protocol Adherence Attestation (GenLayer Intelligent Contract)

An intelligent smart contract built on **GenLayer** that automates the verification and on-chain attestation of clinical trial protocol adherence using decentralized LLM consensus.


## Overview

Clinical trial monitoring often relies on slow, centralized manual audits to confirm whether patient dosing intervals, laboratory thresholds, and medication restrictions follow study protocols. 

This contract implements an autonomous compliance auditor on-chain. It accepts unstructured clinical event descriptions and evaluates them against the study's protocol rules using GenLayer's **Equivalence Principle**, recording an immutable attestation on the blockchain.


## Key Architecture & Design

* **Equivalence Principle Consensus (`gl.eq_principle.prompt_non_comparative`)**: 
  Instead of relying on fragile single-LLM wrappers or deterministic string matching, validators independently evaluate complex medical events against study criteria and reach consensus on a categorical verdict (`COMPLIANT` vs. `VIOLATION`).
* **GenVM State Storage (`TreeMap`)**: 
  Uses GenVM's native `TreeMap` storage to persist verifiable audit trails mapping `patient_id` to the final attestation string.
* **Separation of Read/Write**: 
  Exposes public view methods to query protocol metadata and individual patient audit statuses without gas overhead.


## Contract Interface

### Constructor
* `__init__(protocol_name: str, protocol_rules: str)`
  * Initializes the contract with the study title and protocol rules (e.g., dosing schedules, biomarker cutoffs, prohibited concomitant medications).

### Write Methods
* `evaluate_and_record_event(patient_id: str, event_summary: str) -> str`
  * Prompts the validator network to evaluate whether `event_summary` complies with `protocol_rules`.
  * Persists the record and returns the verdict: `COMPLIANT` or `VIOLATION`.

### View Methods
* `get_attestation(patient_id: str) -> str`: Returns the recorded attestation for a given patient ID.
* `get_protocol_name() -> str`: Returns the active trial protocol name.
* `get_protocol_rules() -> str`: Returns the trial protocol constraints.


## Example Usage

### 1. Initialization Inputs
* **`protocol_name`**: `OncoShield Phase-II Study`
* **`protocol_rules`**: `1. Dose must be administered every 21 days (+/- 2 days). 2. Patient absolute neutrophil count (ANC) must be >= 1.5 x 10^9/L prior to dosing. 3. Concomitant use of strong CYP3A4 inhibitors is strictly prohibited. 4. Patient systolic blood pressure must remain under 160 mmHg.`

### 2. Test Case A (Compliant)
* **`patient_id`**: `PT-101`
* **`event_summary`**: `Patient received cycle 2 on day 21. Pre-dose ANC was 2.0 x 10^9/L. BP 120/80 mmHg. No other medications administered.`
* **Result**: `COMPLIANT`

### 3. Test Case B (Protocol Violation)
* **`patient_id`**: `PT-102`
* **`event_summary`**: `Patient received cycle 2 on day 35. Pre-dose ANC was 1.1 x 10^9/L. Co-prescribed ketoconazole.`
* **Result**: `VIOLATION`


## How to Test on GenLayer Studio

1. Open [GenLayer Studio](https://studio.genlayer.com/).
2. Create a new file named `trial_compliance.py` and paste the contract code.
3. In the **Run & Debug** tab, enter constructor values and click **Deploy**.
4. Invoke `evaluate_and_record_event` with sample patient notes to observe consensus execution.
