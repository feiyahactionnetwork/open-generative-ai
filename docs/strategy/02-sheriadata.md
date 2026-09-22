# SheriaData — Fiscal Forensics and Constitutional Enforcement Infrastructure for Kenya

**Role:** Lead Systems Architect / OSINT Engineer / Constitutional Legal Strategist
**Status:** Executable, with three specification changes that are not optional — two are criminal-liability issues and one is an admissibility issue that determines whether any of the output is worth anything in court.

---

## 2.0 Three corrections that change the architecture

### 2.0.1 You cannot scrape IFMIS. Attempting it is a criminal offence, and you don't need to.

[Certain] Kenya's **Integrated Financial Management Information System (IFMIS)** is a closed government ERP. There is no public IFMIS API and no public IFMIS data export. Accessing it without authorization engages **section 14 of the Computer Misuse and Cybercrimes Act 2018** (unauthorized access), with aggravated penalties where the target is a protected computer system. Credentialed access obtained from a sympathetic insider does not fix this — it converts your organization into the recipient of unauthorized access and exposes the insider to prosecution with no whistleblower statute to shield them (§2.4.3).

**This is not a risk to manage. It is a design constraint. Remove IFMIS scraping from the architecture entirely.**

The replacement is better, legal, and — this is the part most civic-tech projects miss — **generates a second enforcement lever as a side effect**:

> **Industrialized Access to Information requests are a more powerful data-acquisition engine than scraping, because a refusal is itself a justiciable event.**

[Certain] Article 35 of the Constitution grants every citizen the right of access to information held by the State. The **Access to Information Act 2016** operationalizes it: a public entity must respond within **21 days**; refusal or silence is appealable to the **Commission on Administrative Justice** (the Ombudsman), and thence to the High Court.

A scraper that gets blocked produces nothing. **An ATI request that gets refused produces a cause of action.** Build the ATI pipeline as a first-class system component (§2.1.3), not as a manual fallback.

### 2.0.2 Auto-generating court pleadings is unauthorized legal practice and will get your evidence thrown out anyway.

Two independent problems:

**(a) The Advocates Act.** [Certain] Kenya's Advocates Act (Cap 16) makes it an offence for an unqualified person to draw or prepare documents relating to legal proceedings for or in expectation of fee or reward. A platform that generates injunction drafts and petitions for public filing sits squarely in this exposure, and the Law Society of Kenya has both standing and appetite to act on it.

**(b) Admissibility — and this is the higher-value point.** [Certain] **Section 106B of the Evidence Act (Cap 80)** governs the admissibility of electronic records in Kenya. A computer-produced record is admissible only where accompanied by a **certificate** that identifies the electronic record, describes the manner of its production, gives particulars of the device involved, and is signed by a person occupying a responsible official position in relation to the operation of the relevant device or management of the relevant activities.

**A scraped dataset with no s.106B certificate and no chain of custody is inadmissible.** You can be completely, demonstrably right about a KSh 4 billion procurement fraud and lose on an evidential technicality that a first-year litigator would raise in their sleep.

**Almost nobody building civic tech in Kenya designs for s.106B. This is the single highest-leverage insight in this module.**

**Corrected architecture:**
- The platform generates **evidence packs**, not pleadings: a s.106B-compliant certificate, a cryptographic chain of custody from ingestion to output, the source documents in their original form with hashes, the analytical methodology, and a plain-language factual narrative.
- **Pleadings are drawn by admitted advocates** at a partner entity. Structure this as a formal referral arrangement with a public-interest litigation partner (the Katiba Institute model, or an LSK-affiliated public interest chamber).
- Build the **certificate generator and custody chain into the ETL from day one** (§2.1.4). Retrofitting provenance is impossible; you cannot certify a record whose production you did not instrument.

**A second practical warning:** Kenyan courts award costs, and mass-filed boilerplate petitions attract adverse costs and judicial hostility. [Likely] Ten well-evidenced, advocate-drawn petitions with clean s.106B certificates will move more than a thousand auto-generated filings, and the thousand will discredit the project and the cause. **Volume is the wrong objective in litigation.**

### 2.0.3 Zero-knowledge proofs do not hide the thing that gets people killed.

This needs to be said plainly because the consequence of getting it wrong is physical.

[Certain] A ZK proof — Semaphore, a Starknet circuit, any of them — proves a statement without revealing the prover's identity **within the cryptographic protocol**. It does nothing about:

- **Network metadata.** If a source connects from their handset over a Kenyan mobile network to your endpoint, the state does not need to break your circuit. It obtains the call-data record. Carrier-level metadata is the attack, and ZK has no bearing on it.
- **Device forensics.** If the handset is seized, the app's presence, its local storage, and its usage timestamps are all recoverable.
- **The app itself as a marker.** A novel, identifiable, single-purpose anti-corruption app on a phone is an incriminating artifact in a jurisdiction where [Certain] KNCHR and Amnesty Kenya have documented abductions of online critics during 2024–25.
- **Stylometry and content-based deanonymization.** A submission describing events only four people witnessed identifies one of four people regardless of the cryptography.

**Therefore, a two-tier design, and the tiering is a safety requirement, not a product preference:**

| Tier | Source risk | Tooling | Why |
|---|---|---|---|
| **High-risk** — serving officials, insiders, anything that could trigger state reprisal | Severe | **Route to established, audited, widely-used tools: SecureDrop over a Tor onion service; Signal to a published number held by the partner newsroom.** Do not build bespoke. | These tools have a decade of adversarial review, and crucially their user base is large and diverse — presence on a device is not itself evidence of anything. **A bespoke ZK app is worse than SecureDrop here, because it is rarer and therefore more incriminating.** |
| **Low-risk, high-volume** — citizens reporting an unbuilt project, a ghost clinic, an absent water point | Moderate (county-level reprisal) | **Semaphore-style anonymous group membership** over Tor/VPN, with a strict no-log design | Here ZK earns its place: it lets a verified group member (e.g. "a resident of Ward X", "a registered contractor") make a credible attestation without revealing which member, which defeats reprisal-by-elimination in small groups. |

**Design the high-risk path to be boring and proven. Innovate only where failure is survivable.**

---

## 2.1 Data ingestion and anomaly pipeline

### 2.1.1 The real source inventory

[Certain — these are genuinely public. Quality and machine-readability vary enormously and the system must be built for degraded PDF sources, not clean APIs.]

| Source | Publisher | Format | What it yields | Difficulty |
|---|---|---|---|---|
| **PPIP** (`tenders.go.ke`) | Public Procurement Regulatory Authority | Web, semi-structured | Tender notices, awards, contract values, buyer/vendor names | Medium — inconsistent completeness is the core data-quality problem |
| **Budget Implementation Review Reports** | Controller of Budget | Quarterly PDF, national + all 47 counties | Actual vs budgeted expenditure by vote, absorption rates, pending bills | High — table extraction from PDF |
| **Audit reports** | Office of the Auditor-General | Annual PDF per entity | Audit queries, unsupported expenditure, ineligible payments, pending bills | High — but the **highest-value text corpus in the country** |
| **Public debt** | Central Bank of Kenya | Weekly bulletin, Annual Public Debt Management Report | Domestic debt by holder, external debt by creditor, debt service schedule | Low-medium |
| **QEBR / BPS / Budget Estimates** | National Treasury | PDF/XLSX | Fiscal framework, revenue performance, appropriations | Medium |
| **Company registry + beneficial ownership** | Business Registration Service via eCitizen | Paid lookup | Directors, shareholders, registered address — **the join key for the whole network analysis** | Medium; costed per lookup, access to the BO register is restricted |
| **Debarment list** | PPRA | Web | Debarred suppliers | Low |
| **Kenya Gazette** | Government Printer | PDF | Appointments, notices, compulsory acquisitions | Medium |
| **Hansard + PAC/PIC reports** | Parliament | PDF | Committee findings, follow-up on audit queries | High |
| **Case law** | Kenya Law (`kenyalaw.org`) | Web | Precedent, existing litigation, judgments against entities | Low-medium |
| **IEBC party funding** | IEBC | PDF | Political finance | Medium |
| **ATI responses** | **Your own pipeline** (§2.1.3) | Whatever arrives | **The gap-filler and the enforcement lever** | — |

**Note what is absent: IFMIS.** Per §2.0.1.

### 2.1.2 ETL architecture

```
┌─────────────────────────────────────────────────────────────────┐
│ ACQUISITION                                                      │
│  ├─ Scheduled crawlers (PPIP, PPRA, Gazette, Kenya Law)         │
│  │   • robots.txt respected; rate-limited; public endpoints only│
│  ├─ Document watchers (COB, OAG, Treasury, CBK release pages)   │
│  ├─ BRS lookup worker (queued, cost-metered)                    │
│  └─ ATI request engine  ◄── §2.1.3                              │
└────────────────────────────┬────────────────────────────────────┘
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│ IMMUTABLE RAW LANDING — write-once, never mutated               │
│  • Original bytes + SHA-256 + fetch timestamp + HTTP headers    │
│  • WARC capture for web sources (full request/response record)  │
│  • Arweave pin of the hash manifest (§2.4.2)                    │
│  • **This layer is the evidentiary substrate. It is the reason  │
│    a s.106B certificate can later be issued.**                  │
└────────────────────────────┬────────────────────────────────────┘
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│ EXTRACTION                                                       │
│  ├─ Born-digital PDF → pdfplumber / Camelot table extraction    │
│  ├─ Scanned PDF → OCR (Tesseract/PaddleOCR) + confidence scores │
│  ├─ Layout-aware parse for complex tables → LayoutLM-family     │
│  ├─ **Every extracted cell retains: source doc hash, page,      │
│  │   bbox, extraction method, confidence.**                     │
│  └─ Low-confidence cells → human verification queue             │
└────────────────────────────┬────────────────────────────────────┘
                             ▼
┌─────────────────────────────────────────────────────────────────┐
│ RECONCILIATION & ENTITY RESOLUTION — the hardest part            │
│  ├─ Vendor name normalization (Kenyan naming is filthy:         │
│  │   "ABC Ltd", "A.B.C. Limited", "ABC LTD.", typos, aliases)   │
│  ├─ Blocking + fuzzy match (Jaro-Winkler / token-set) then a    │
│  │   trained pairwise classifier; **never auto-merge above a    │
│  │   consequence threshold — route to human review**            │
│  ├─ Join to BRS: directors, shareholders, registered address,   │
│  │   incorporation date, phone                                  │
│  └─ PEP register construction (§2.2.4)                          │
└────────────────────────────┬────────────────────────────────────┘
                             ▼
┌──────────────────┬──────────────────┬───────────────────────────┐
│ Postgres +       │ Neo4j / Memgraph │ Object store               │
│ TimescaleDB      │ entity graph     │ documents + WARC           │
│ (fiscal series)  │                  │                            │
└──────────────────┴──────────────────┴───────────────────────────┘
                             ▼
              ANOMALY ENGINE (§2.2) → EVIDENCE PACKS (§2.3)
```

**Engineering notes that matter more than they look:**

- **Entity resolution is the project.** [Certain] Every procurement-forensics effort worldwide founders on the same rock: you cannot detect a vendor cartel if you cannot reliably tell that "Jaribu Supplies Ltd" and "JARIBU SUPPLIES LIMITED" are one company and that both share a director with "Mema Holdings". Budget **40% of engineering effort** here. It is unglamorous and it is the whole system.
- **Never mutate the raw layer.** Corrections happen in a derived layer with a full lineage record. The moment you edit raw data you have destroyed your s.106B position for every record downstream.
- **Confidence must propagate.** An OCR cell at 0.71 confidence that feeds an anomaly flag must surface that 0.71 in the evidence pack. Laundering uncertainty is how a project like this gets destroyed by one wrong public accusation.

### 2.1.3 The ATI request engine — data acquisition as enforcement

This is the component that distinguishes SheriaData from every dashboard that came before it.

```
Gap detector ──► Request generator ──► Filing ──► 21-day clock ──► Response
     │                  │                              │               │
 identifies         templates an          tracked       ▼               ▼
 missing data       Art.35/ATI 2016      per-entity  No response?   Parsed into
 (e.g. a contract   request naming        SLA        Refusal?       raw landing
 awarded with no    the specific                         │          (with full
 vendor detail)     record sought                        ▼          provenance)
                                              ┌──────────────────┐
                                              │ CAJ (Ombudsman)  │
                                              │ appeal package   │
                                              │ auto-assembled   │
                                              └────────┬─────────┘
                                                       ▼
                                              High Court referral
                                              (advocate-drawn)
```

**Why this is strategically superior to scraping:**
1. It is **entirely legal** and puts the burden of production on the state.
2. A refusal is a **documented, justiciable act of concealment** — often more probative than the underlying record.
3. It produces a public, auditable record of which entities obstruct. **The refusal rate becomes a governance metric in its own right** and feeds directly into the county scorecard (§2.3.2).
4. It cannot be blocked by a firewall rule.

**Request templating must be specific.** [Certain] Broad, fishing-expedition ATI requests are lawfully refusable as unreasonably burdensome. Requests must name the record: a specific tender number, a specific payment voucher, a specific contract. The gap detector's job is to produce *that specificity* from the structured data you already hold.

### 2.1.4 Chain of custody and the s.106B certificate generator

Every record carries an append-only lineage:

```json
{
  "record_id": "ppip:2025:TN-4471:award",
  "source_uri": "https://tenders.go.ke/...",
  "fetch_ts_utc": "2025-11-04T06:12:33Z",
  "raw_sha256": "9f2c...",
  "warc_ref": "s3://raw/warc/2025-11-04/ppip-0612.warc.gz#offset=88213",
  "arweave_manifest_tx": "kR3n...",
  "extraction": {
    "method": "pdfplumber@0.11.4 + camelot@0.11",
    "page": 14, "bbox": [72,318,523,402],
    "confidence": 0.94,
    "operator_verified": true,
    "verified_by": "custodian-07", "verified_ts": "2025-11-05T09:20:11Z"
  },
  "transforms": ["normalize_currency:KES", "entity_resolve:v3.2"],
  "s106b_eligible": true
}
```

The **certificate generator** renders, for any selected record set, a document meeting the s.106B requirements: identification of the electronic record, the manner of production, particulars of the devices and systems involved, and a signature block for the **designated Records Custodian** — a named human holding a responsible official position with respect to the system's operation.

**Organizational requirement: appoint a Records Custodian on day one and never let the role lapse.** The certificate requires a signatory with genuine responsibility for the system. This is a hiring decision that determines evidentiary viability.

---

## 2.2 The anomaly mathematics — and why Benford's Law is not the centrepiece

### 2.2.1 Benford's Law: triage only, and never in an evidence pack

**I disagree with putting Benford's first-digit test at the centre of the detection stack.** Reasoning:

[Certain] Benford's Law states that for many naturally-occurring datasets spanning several orders of magnitude, the leading digit *d* occurs with probability

$$P(d) = \log_{10}\!\left(1 + \frac{1}{d}\right), \quad d \in \{1,\dots,9\}$$

Testing is straightforward — chi-square, or the mean absolute deviation, or Kuiper's test for the circular case.

**The risks in procurement data specifically:**
1. **Threshold effects violate the scale-invariance assumption.** Procurement data is deliberately clustered around statutory thresholds. That clustering alone produces Benford deviation with no fraud present.
2. **Price anchoring and round numbers.** Human-negotiated contract values cluster at round figures. Benford deviation, no fraud.
3. **Narrow range.** Benford requires several orders of magnitude. A single procurement category often spans less than two.
4. **It is not evidence.** [Likely] No Kenyan court has accepted a Benford deviation as probative of fraud, and it would be trivially attacked by any competent defence expert on grounds (1)–(3).

**Correct role: a cheap, high-recall triage filter that ranks datasets for human attention. It never appears in an evidence pack, never in a public accusation, never in a pleading.**

### 2.2.2 The primary engine: a Kenyan Corruption Risk Index

[Certain] The validated state of the art in quantitative procurement forensics is the **Corruption Risk Index** family developed by Fazekas and colleagues at the Government Transparency Institute, built and tested on EU TED procurement data. It works because its components are **legally legible** — each one corresponds to a specific procedural irregularity a court can understand — rather than statistically exotic.

Adapted to Kenya's **Public Procurement and Asset Disposal Act 2015** and its 2020 Regulations:

| Red flag | Computation | Why it signals |
|---|---|---|
| **Single bidding** | Awards where bidders = 1 in an open procedure | The strongest single predictor in the literature. Competition was eliminated. |
| **Non-open procedure without justification** | Direct procurement / restricted tendering where PPADA conditions are not evidenced | PPADA restricts these to defined circumstances |
| **Short submission window** | Days between advertisement and deadline, below the statutory minimum or in the bottom decile | Compresses the window so only a pre-briefed bidder can respond |
| **No advertisement** | Award with no traceable PPIP notice | |
| **Threshold bunching** | See §2.2.3 | Splitting to evade a procedure |
| **New-entrant winner** | Vendor incorporated < 12 months before award, winning above a value percentile | Classic shell pattern |
| **Winner concentration** | Share of a buyer's awards to a single vendor over a rolling window | Capture |
| **Bid-CV compression** | Coefficient of variation of bid prices below the category's 5th percentile | Cover bidding — losers bid just-high-enough |
| **Fiscal year-end rush** | Award in the final 14 days of the FY (Kenya's FY ends 30 June) | Known absorption-driven leakage window |
| **Price residual** | Hedonic regression of unit price on item/qty/region/time; flag residuals > p95 | Inflation padding |
| **Contract amendment inflation** | Cumulative variation orders as a % of original value | Bid low, amend up |
| **Debarment / tax-status mismatch** | Winner on PPRA debarment list, or registered in an opaque jurisdiction | |

**Compose these as a weighted index, but publish the components, never only the composite.** A composite score is unfalsifiable and indefensible in court. The components are individually checkable, individually rebuttable, and individually map to a provision of the PPADA — which is exactly what an advocate needs.

### 2.2.3 Threshold bunching — the most legally legible test in the stack

This is worth isolating because it is statistically rigorous, visually obvious to a judge, and directly maps to a specific statutory offence (contract splitting to evade a procurement method).

[Certain] PPADA and its regulations set value thresholds determining which procurement method is permissible. If contracts are being split to stay below a threshold *T*, the density of contract values will show a discontinuous spike immediately below *T* and a deficit immediately above.

Formally, apply a **McCrary density discontinuity test**: estimate the density $f(v)$ of contract values on either side of $T$ via local linear regression on a fine bin histogram, and test

$$\theta = \ln f^-(T) - \ln f^+(T) = 0$$

A significantly positive $\hat\theta$ is evidence of manipulation of the running variable. The **excess mass** estimator quantifies the magnitude:

$$b = \frac{\sum_{j \in [T-h,\,T)} \left(c_j - \hat{c}_j\right)}{\hat{c}_T}$$

where $c_j$ is observed bin count and $\hat c_j$ the counterfactual fitted excluding the manipulation window.

**Why this is the best test you have:** it produces a single histogram a judge can look at and immediately understand, it has a well-established econometric literature behind it, the null hypothesis is clean, and the alleged conduct — splitting — is a named irregularity under the Act. **This is the test to lead with in the first litigation.**

### 2.2.4 Network analysis — finding the cartel, not the outlier

Construct a heterogeneous graph:

- **Nodes:** procuring entities, vendors, natural persons (directors/shareholders from BRS), addresses, phone numbers, bank accounts where disclosed
- **Edges:** `AWARDED_TO`, `DIRECTOR_OF`, `SHAREHOLDER_OF`, `SHARES_ADDRESS`, `SHARES_PHONE`, `CO_BID_WITH`, `RELATED_TO` (PEP linkage)

**The algorithms that earn their place:**

1. **Bipartite projection + co-bidding similarity.** Project the vendor–tender bipartite graph onto vendors. Two vendors that repeatedly appear in the same tenders but *never win against each other* are a bid-rotation signature. Compute, for vendor pairs $(i,j)$:

   $$S_{ij} = \frac{|T_i \cap T_j|}{\sqrt{|T_i||T_j|}}, \qquad R_{ij} = \frac{\text{alternating wins in } T_i \cap T_j}{|T_i \cap T_j|}$$

   High $S_{ij}$ with $R_{ij}$ near a perfect alternation is the cartel fingerprint. **This finds rings that no per-contract test can see**, because each individual contract looks competitive.

2. **Community detection (Leiden).** Over the director/shareholder/address graph, to surface clusters of nominally independent companies that are one entity.

3. **Betweenness centrality** on the person layer — identifies the small number of individuals who bridge otherwise-separate vendor clusters. These are the brokers, and they are the highest-value investigative targets.

4. **Shell-company scoring** — incorporation recency, director count of 1–2, shared registered address with n other entities, no prior award history, director who is director of >k companies. A simple logistic model on these features outperforms anything exotic.

5. **PEP linkage.** Construct the PEP register from the Kenya Gazette (appointments), IEBC records, parliamentary rolls, and county executive listings. Link via BRS director/shareholder records and name-plus-identifier matching.

**Critical discipline on PEP linkage:** name matching alone is grossly insufficient — Kenyan naming conventions produce enormous false-positive rates. **Require a second identifier (national ID fragment, date of birth, known address) before any PEP flag leaves the system.** A false PEP accusation is defamation, is personally devastating to an innocent person, and would end the project. Route every PEP flag through mandatory human review with a named reviewer recorded in the lineage.

### 2.2.5 Debt mechanics — the macro layer

Track from CBK and Treasury publications:

- **Debt service to ordinary revenue ratio.** This is the number that drives the politics. [Likely] Kenya's debt service has consumed on the order of 60%+ of ordinary revenue in recent fiscal years — the structural fact underneath every Finance Bill fight. **Publish it monthly, tracked against budget, with the arithmetic shown.**
- **Domestic debt by holder** — the banking-sector concentration, and the crowding-out of private credit.
- **External debt by creditor class** — multilateral vs bilateral vs commercial, and the average cost of each. Commercial refinancing at high single-digit or double-digit coupons versus concessional multilateral money is the core of the fiscal problem and it is under-communicated.
- **Maturity wall** — a forward schedule of principal and coupon obligations by quarter.
- **Pending bills** — cross-referenced between Controller of Budget reports and Auditor-General findings. [Likely] Pending bills to government suppliers run into the hundreds of billions of shillings. **This is the direct transmission channel from fiscal mismanagement to private-sector job destruction**, and it is the most under-covered number in Kenyan public finance.

---

## 2.3 The enforcement engine — corrected

### 2.3.1 What the system actually emits

| Output | Recipient | Legal character |
|---|---|---|
| **Evidence pack** — s.106B certificate + source documents + hashes + methodology + factual narrative + uncertainty statement | Partner advocates; journalists; PPRA; EACC; Auditor-General; parliamentary committees | Admissible evidentiary bundle. **Not a pleading.** |
| **ATI request + CAJ appeal package** | Public entities; Commission on Administrative Justice | Statutory process under ATI Act 2016 |
| **County Fiscal Scorecard** (§2.3.2) | Public, free, permanent | Journalism / public information |
| **Referral memorandum** | Partner advocates | Instructions to counsel — **privileged, and drawn by non-advocates lawfully because it is instruction, not pleading** |
| **Debt monitor** | Public | Public information |

**Standing is not the obstacle.** [Certain] Article 22 of the Constitution grants broad standing to any person to institute proceedings alleging a Bill of Rights violation, including in the public interest; Article 258 does the same for any alleged contravention of the Constitution. Kenya has unusually permissive public-interest standing. **The obstacle is evidentiary quality and litigation capacity, which is exactly what this architecture targets.**

Substantive anchors for partner counsel: **Article 35** (access to information), **Article 201** (principles of public finance — openness, accountability, public participation), **Article 226** (accounts and audit), **Article 227** (procurement must be fair, equitable, transparent, competitive and cost-effective), and the **PFM Act 2012** and **PPADA 2015** implementing them.

### 2.3.2 The County Fiscal Scorecard — the public good that funds nothing and matters most

47 counties, published continuously, free, permanently archived on Arweave:

| Dimension | Metric |
|---|---|
| **Transparency** | ATI response rate; median days to respond; refusal rate; publication compliance under PFM Act |
| **Procurement integrity** | CRI component rates: single-bidding %, threshold bunching $\hat\theta$, new-entrant win rate, winner concentration |
| **Execution** | Development budget absorption; recurrent-to-development ratio |
| **Obligations** | Pending bills as % of annual budget; median payment days to suppliers |
| **Audit** | Unresolved audit queries; value of unsupported expenditure |

**Why the scorecard is the strategic core rather than a nice-to-have:** it converts governance quality into a **number with a comparative ranking**, published on a fixed cadence, that a county government cannot ignore because its own suppliers, its own voters, and its own lenders read it. A dashboard nobody reads changes nothing. **A ranked league table that a governor's political opponent quotes changes behaviour.**

Note also: the pending-bills and payment-days columns have a second, commercial life — they are the raw input to a receivables-pricing product that could fund the entire non-profit layer. That is a separate business and is flagged here only as the obvious adjacency.

### 2.3.3 Why every previous civic tool failed, and what specifically is different here

| Prior tool / class | What it did well | Why it did not produce systemic change |
|---|---|---|
| **Ushahidi** (Kenyan-born, 2008) | Genuinely world-class crowdmapping software, globally adopted | [Certain] Produced **evidence, not consequences.** There was no mechanism connecting a map pin to an enforceable obligation. |
| **Kenya Open Data Initiative** (launched 2011) | Early, ambitious, internationally celebrated | Went stale repeatedly. **Publishing machine-unreadable PDFs satisfies the legal duty while defeating its purpose.** Publication ≠ accountability. |
| **BudgIT (Nigeria), Code for Africa, Mzalendo, Africa Uncensored, The Elephant** | Excellent data journalism and parliamentary monitoring | No enforcement leverage, no independent revenue, no litigation capacity |
| **Global open-contracting portals (OCDS adopters)** | Standardized data | Standardization without analysis and without a plaintiff |
| **Generic anti-corruption dashboards** | Visibility | **Monitor outputs the state has already decided.** No position in the decision loop, and no cashflow — so they die on the grant cycle. |

**The five structural failures, and SheriaData's answer to each:**

| Failure | Answer |
|---|---|
| **Evidence without consequences** | Evidence packs built to s.106B admissibility standard, routed to advocates with Article 22/258 standing |
| **No position in the decision loop** | The ATI engine forces disclosure *before* and *during* execution, not after the audit two years later |
| **Grant-cycle mortality** | A commercial layer (data licensing to lenders, insurers, journalists; procurement-risk screening sold to private buyers and development-finance institutions) cross-subsidizes the free public layer. **Design the revenue line in v1, not v3.** |
| **Centralization / takedown** | §2.4.2 |
| **Atomized outrage that doesn't aggregate into a claim** | The scorecard creates a persistent, comparative, quotable artifact that outlives a news cycle |

---

## 2.4 Threat model and resilience

### 2.4.1 Adversary capabilities — realistic assessment

| Capability | Assessment | Impact |
|---|---|---|
| **Source-side API/portal deprecation** | **High likelihood.** The cheapest countermeasure available to the state: degrade PPIP, stop publishing a report series, move to scanned images. | **Primary threat.** Countered by the ATI engine (refusal becomes justiciable) and by archiving everything on acquisition. |
| **DNS blocking / domain seizure** | Medium | Countered by §2.4.2 |
| **Legal harassment** — Computer Misuse and Cybercrimes Act 2018 ss.22/23 (false publication), defamation suits, regulatory pressure | **High likelihood** | [Certain] These provisions have been used against online critics; the High Court upheld most of the Act's challenged provisions following the Bloggers Association of Kenya litigation. **Countered by accuracy discipline, not by technology.** Every public claim must be sourced, hashed, and rebuttable. |
| **Targeting of individual staff** — including abduction | [Certain] Documented against online critics in 2024–25 by KNCHR and Amnesty Kenya | **This is the threat that matters most and it has no technical mitigation.** See §2.4.3. |
| **Network-level disruption** | [Certain] Kenya experienced significant nationwide connectivity degradation on 25 June 2024 (reported by NetBlocks); the subsea-cable explanation offered at the time was widely disputed. A full shutdown has not been the pattern; degradation has. | Countered by §2.4.2 offline/mesh paths |
| **Carrier metadata access** | High capability, low cost to the state | **The reason ZK is not an anonymity solution (§2.0.3)** |
| **Device seizure and forensics** | High | Minimal local storage; no bespoke high-risk app |

### 2.4.2 Resilience architecture

```
  PUBLICATION (must survive takedown)
  ├─ Arweave        : permanent, pay-once. Every evidence pack, every
  │                   scorecard snapshot, every raw-manifest hash.
  │                   **Genuinely the right tool here** — the threat is
  │                   erasure of a public record, which is exactly what
  │                   permanent storage defeats.
  ├─ IPFS + pinning : fast retrieval layer over the Arweave record
  ├─ Git mirrors    : GitHub + Codeberg + a self-hosted Forgejo, in
  │                   three jurisdictions. Datasets as versioned repos.
  └─ Static mirrors : multiple providers, multiple TLDs, rotating

  ACCESS (must survive blocking)
  ├─ Tor onion service (v3) — primary censorship-resistant endpoint
  ├─ Tor bridges: obfs4 + Snowflake + meek  (note: classic domain
  │   fronting is largely dead — major cloud providers disabled it)
  ├─ Published mirror list signed with a long-lived offline key,
  │   distributed via Matrix, Telegram channel, and email list
  └─ Full dataset torrents + magnet links — unblockable, and it makes
      the archive *other people's* problem to preserve

  COMMS
  ├─ Matrix (self-hosted Synapse, federated) — team + partner comms
  ├─ Signal — high-risk source contact, via the partner newsroom
  ├─ SecureDrop over onion — high-risk submissions (§2.0.3)
  └─ Briar — offline/mesh fallback for field teams during disruption

  ORGANIZATIONAL
  ├─ Publishing and archival entity incorporated outside Kenya
  ├─ Kenyan entity kept thin: analysis and community, minimal data
  ├─ Partner advocates hold the litigation function (§2.0.2)
  └─ Board and advisory with international standing — reputational
      cost of targeting the organization is itself a deterrent
```

**Decentralization-first, with the caveat:** distribute the *published record*, not the *raw pipeline*. The pipeline needs a trusted, instrumented, single chain of custody or the s.106B position collapses. **Decentralize the output; centralize and instrument the evidence.** These are in tension and the tension must be resolved deliberately rather than by defaulting to "decentralize everything."

### 2.4.3 The safety obligation, stated directly

[Certain] Kenya has **no comprehensive whistleblower protection statute.** A Whistleblower Protection Bill has been pending for years. The Witness Protection Act 2006 protects witnesses in proceedings, and the Bribery Act 2016 contains partial protections, but a civil servant who leaks a procurement file to a platform is **legally exposed and practically unprotected.**

Operating obligations that follow:

1. **Never solicit unlawfully obtained material.** It compromises your evidentiary position, exposes the source, and exposes the organization.
2. **Tell sources the truth about their risk, in plain language, before they submit.** Including that ZK does not protect their network metadata.
3. **Route high-risk sources to proven tools and to a partner newsroom with existing source-protection practice and legal cover.** Not to a bespoke app.
4. **Minimize retention on every high-risk path.** What you do not hold cannot be seized or compelled.
5. **Fund legal defence for staff before you need it.** Retain counsel on a standing basis.
6. **Do not publish an accusation you cannot fully evidence.** The project's survival depends entirely on never being demonstrably wrong in public, and a person's safety may depend on it too.

---

## 2.5 Winning Execution Strategy

**Build a legally-acquired, forensically-instrumented public finance record whose output is admissible evidence and whose data-acquisition mechanism is itself an enforcement action.**

The five decisions that define it, each bypassing a named failure:

1. **ATI requests replace IFMIS scraping.** Removes criminal exposure, and converts every refusal into a justiciable act of concealment. **Data acquisition and enforcement become the same system.**
2. **s.106B certification and chain of custody are built into the ETL from the first commit.** This is the difference between a dashboard and evidence, it cannot be retrofitted, and almost nobody else in this space does it.
3. **Evidence packs for advocates, never auto-generated pleadings.** Avoids the Advocates Act, avoids adverse costs, avoids discrediting the cause with boilerplate volume.
4. **The Fazekas-style Corruption Risk Index and threshold-bunching tests carry the analysis; Benford is triage only and never leaves the building.** Every published flag maps to a specific provision of the PPADA that a court already understands.
5. **Two-tier source protection: proven tools for high-risk sources, ZK only where failure is survivable.** This is a safety decision, not a product decision.

**And the one that determines whether it still exists in three years:** a **commercial data layer in v1** — procurement-risk screening and county payment-behaviour data sold to development-finance institutions, lenders, insurers and private buyers — cross-subsidizing a permanently free public scorecard. Every civic-tech project in this lineage died on the grant cycle. **Revenue is not a compromise of the mission; it is the only thing that has ever made one of these survive a change of government.**

**Sequence:** ATI engine + raw-landing + custody chain first (months 0–4, this is the foundation and it is unglamorous). PPIP/COB/OAG extraction and entity resolution next (months 3–8 — entity resolution is 40% of the work). CRI and bunching tests (months 6–10). First scorecard publication month 9. **First litigation only when one case is unimpeachable** — one clean win on threshold bunching with a certified evidence bundle establishes the precedent and the credibility that a hundred weak filings would destroy.
