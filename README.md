
---

## Step-by-Step Plan (Detailed)

### Step 0 — Environment Setup
**Goal:** Ensure clean workspace and repeatability.
- Open the repo root folder in VS Code
- Verify Git is working (`git status`)
- Create folders: `logs/`, `analysis/`, `docs/`
- Add a `.gitignore` to avoid committing huge or sensitive files (optional)

**Output:**
- Repo structure committed to GitHub
- README + folder layout visible to reviewers

---

### Step 1 — Acquire Log Samples
**Goal:** Start with realistic telemetry (public datasets or sanitized samples).
I will gather:
- One dataset focused on network traffic + DNS
- One dataset with IDS alerts (JSON)
- One dataset with Windows authentication logs
- One dataset with endpoint network telemetry (EDR-style)

**Output:**
- Logs placed into `logs/<source>/`
- `docs/` contains a short note describing dataset origin and scope

---

### Step 2 — Ingest Logs into Splunk
**Goal:** Make logs searchable and time-correct.
In Splunk I will:
- Add data using **Upload** (quick start) or forwarders later
- Assign each dataset a **sourcetype** (dns, firewall, ids, edr, windows, proxy)
- Confirm timestamps parse correctly (events appear in correct time order)
- Confirm fields are extracted (JSON fields appear automatically for IDS/EDR)

**Output:**
- `analysis/ingestion-notes.md` (optional) with what sourcetypes were used and issues found

---

### Step 3 — Field Extraction (Parsing)
**Goal:** Turn raw strings into usable fields.
For each log type I will ensure:
- `src_ip`, `dest_ip`, `dest_port`, `dest_domain`, `user`, `hostname` exist when applicable
- Actions (allow/deny) map consistently to `action`
- Protocol maps consistently to `protocol`

If needed, I will:
- Use regex extractions for flat text logs (firewall/proxy)
- Use JSON extraction for IDS/EDR logs
- Use XML parsing strategy for Windows event logs (or pre-parsed exports)

**Output:**
- `analysis/normalization.md` updated with raw → normalized field mapping

---

### Step 4 — Normalization (SIEM Thinking)
**Goal:** Make different sources “speak the same language.”
I will define a minimal common schema:
- `src_ip`, `dest_ip`, `dest_port`
- `dest_domain` (when DNS/proxy available)
- `user`, `hostname`
- `action`, `protocol`
- `process_name` (EDR)

**Output:**
- `analysis/normalization.md` with:
  - field mapping table
  - assumptions
  - known gaps

---

### Step 5 — Baseline Queries (Start Analyzing)
**Goal:** Understand normal vs abnormal for this dataset.
I will run and document baseline queries such as:
- Top talkers by `src_ip`
- Most frequent `dest_domain`
- Top `dest_ip` by bytes
- Most common ports
- Top IDS signatures (if IDS exists)

**Output:**
- `docs/queries.md` with baseline SPL
- Screenshots or notes of top results

---

### Step 6 — Investigation Workflow (Repeatable Hunting)
**Goal:** Reconstruct suspicious activity using pivots.
For any suspicious host, I will follow this pivot sequence:

1. Identify suspicious `src_ip` (volume, frequency, anomalies)
2. Pivot to DNS:
   - repeated lookups
   - suspicious domains
   - NXDOMAIN patterns
3. Pivot to firewall/flow:
   - short sessions
   - repeated outbound connections
   - unusual ports/destinations
4. Pivot to IDS:
   - alert signatures
   - severity and categories
5. Pivot to EDR:
   - process + user attribution
   - destination confirmation
6. Pivot to Windows auth:
   - logon anomalies around the same time window

**Output:**
- `analysis/correlation-workflows.md`
- A clear “chain of evidence” from network → identity → endpoint

---

### Step 7 — Build Mini Case Studies (Findings)
**Goal:** Produce consultant-grade written deliverables.
For each case study I will include:
- Scope and time window
- Key observations (with evidence)
- Hypotheses (conservative language; no overclaiming)
- Risk/impact statement
- Recommended next steps (additional logs to confirm)
- Candidate SIEM detections (rules/thresholds)

**Output:**
- `analysis/findings/case-01.md` etc.

---

### Step 8 — Candidate Detections (Operationalization)
**Goal:** Convert investigation lessons into SIEM logic.
Examples:
- DNS beaconing threshold (count per time window)
- Port scan detection (distinct ports per dest host)
- Suspicious outbound TLS patterns (short sessions at fixed intervals)
- Correlation: IDS alert + EDR process + outbound firewall allow

**Output:**
- `docs/queries.md` section: “Candidate Detections”
- Optional: `analysis/detection-rationale.md`

---

## Definition of Done (What “Finished” Looks Like)
This project is complete when:
- Logs from at least 3 sources are ingested (DNS + firewall + one of IDS/EDR/Windows)
- Key fields are normalized (`src_ip`, `dest_ip`, `dest_domain`, `user`, `hostname`)
- At least 1 full investigation story is documented end-to-end
- A small library of reusable queries exists in `docs/queries.md`
- README clearly explains the workflow and deliverables

---

## Notes on Ethics & Safety
- No malware execution is required for this lab
- Datasets will be public, simulated, or sanitized
- Findings are evidence-based and written conservatively

---
