# Cross-Disciplinary Operational Briefing — Index

Four named ventures. **They are not one strategy.** Read §0 before the modules;
it is the part most likely to change what you do with your money.

| # | Venture | Verdict | Capital to first proof | Non-decaying buyer |
|---|---------|---------|------------------------|--------------------|
| 1 | **[KaziMesh](01-kazimesh.md)** — Nairobi bypass transport | **Strongest of the four. Build this one.** Real arbitrage, real regulatory path, node payback ~1.5 months. | ~$180k Phase 0 / ~$1.9M to 12 months | Consumers, directly |
| 2 | **[SheriaData](02-sheriadata.md)** — fiscal forensics | **Executable, after removing two criminal-liability exposures and one admissibility defect.** | ~$400k–700k first year | DFIs, lenders, insurers (funding a free public layer) |
| 3 | **[Solid-state battery IP](03-solid-state-battery-ip.md)** | **Invention is real and worth $1–5B. Four of your five targets are impossible.** The Musk acquisition premise is inverted. | ~$5.4M / 27 months to a credible deal | eVTOL, defence, then six automotive bidders |
| 4 | **[Global Macro Engine](04-global-macro-engine.md)** | **Buildable. But your named market is the most crowded in geospatial and your named business model decays by construction.** | ~$900k Phase 0 | **CBAM compliance verification** |

---

## §0 — The assessment you probably don't want

### 0.1 Five specified targets are impossible, and four of them are impossible by arithmetic

Not "hard." Not "ambitious." Arithmetically foreclosed. Each is checkable in under five minutes by the first competent technical diligence team you meet, and finding out there instead of here is the expensive route.

| Spec | Reality | Where |
|---|---|---|
| **1,100 Wh/kg cell** | Requires a cathode active-material mass fraction of **1.45**. The maximum possible value is 1.0. Only Li-S numerically permits it, at mass fractions and cycle lives nobody has achieved. **Defensible: 420–480 Wh/kg.** | [§3.0.1](03-solid-state-battery-ip.md#301-1100-whkg-at-the-cell-level-is-arithmetically-impossible-for-this-chemistry) |
| **3-minute charging** | At 5 mAh/cm² that is **100 mA/cm²**. The highest critical current density anyone has ever reported is 10–15 mA/cm². **Off by 10–100×. Defensible: 12–18 min.** | [§3.0.2](03-solid-state-battery-ip.md#302-three-minute-charging-is-blocked-by-critical-current-density-by-a-factor-of-10100) |
| **$65 BOM Wi-Fi 7 + 60 GHz + solar + NVMe node** | That device is $600–1,100. **$65 is achievable — for a Wi-Fi 6 leaf node, no solar, no NVMe.** The fix is node tiering, not cost-engineering an impossible device. | [§1.0.1](01-kazimesh.md#1001-wi-fi-7-is-the-wrong-radio-it-buys-nothing-and-costs-25) |
| **Ad-funded (CPM) free data** | Kenyan CPMs yield **$0.0021/GB** against a delivery cost of $0.05–0.09/GB. **Short by 25–40×. This is exactly what killed BRCK Moja.** | [§1.0.3](01-kazimesh.md#1003-ad-funded-free-data-is-arithmetically-dead-the-numbers-not-the-opinion) |
| **$100B forced acquisition** | The largest battery-tech acquisition in Tesla's history was **Maxwell at ~$218M**. Tesla open-sourced its patents in 2014; SpaceX deliberately does not patent. **$100B is ~460× the comp.** | [§3.3.1](03-solid-state-battery-ip.md#331-the-100b-valuation-against-comparables) |

### 0.2 Two specifications create criminal liability

- **Scraping IFMIS** engages **s.14 of the Computer Misuse and Cybercrimes Act 2018** (unauthorized access). IFMIS is a closed government ERP with no public API. Obtaining insider credentials makes it worse, not better, and exposes a source with no whistleblower statute to protect them. → [§2.0.1](02-sheriadata.md#201-you-cannot-scrape-ifmis-attempting-it-is-a-criminal-offence-and-you-dont-need-to)
- **Auto-generating court pleadings** engages the **Advocates Act (Cap 16)** on unauthorized practice, and attracts adverse costs. → [§2.0.2](02-sheriadata.md#202-auto-generating-court-pleadings-is-unauthorized-legal-practice-and-will-get-your-evidence-thrown-out-anyway)

### 0.3 One omission is worse than all the impossible targets combined

**Section 106B of Kenya's Evidence Act (Cap 80).** Electronic records are admissible only with a certificate identifying the record, describing the manner of production, and giving particulars of the systems involved, signed by a person in a responsible official position.

**A scraped dataset with no s.106B certificate and no chain of custody is inadmissible.** You can be completely right about a KSh 4 billion fraud and lose on an evidential point a first-year litigator raises in their sleep. Provenance cannot be retrofitted — you cannot certify the production of a record you did not instrument.

**Almost nobody building civic tech in Kenya designs for this.** It is the highest-leverage single insight in the briefing. → [§2.0.2](02-sheriadata.md#202-auto-generating-court-pleadings-is-unauthorized-legal-practice-and-will-get-your-evidence-thrown-out-anyway)

### 0.4 One safety correction, stated plainly because the consequence is physical

**Zero-knowledge proofs do not hide the thing that gets people abducted.** ZK hides identity *within the protocol*. It does nothing about carrier metadata, device forensics, or stylometry. And a novel single-purpose anti-corruption app is *itself* an incriminating artifact on a seized phone.

**For a high-risk Kenyan source, a bespoke ZK app is worse than SecureDrop over Tor** — precisely because it is rare, and rarity is what identifies you. Innovate only where failure is survivable. → [§2.0.3](02-sheriadata.md#203-zero-knowledge-proofs-do-not-hide-the-thing-that-gets-people-killed)

### 0.5 The bundling is itself a strategic error

Four ventures, four cold starts, no shared team, capital structure, regulator, or customer.

**If you run one, run KaziMesh.** It is the only one where you can be taking revenue inside twelve months, and the only one whose failure modes are fully mapped by a decade of prior Kenyan attempts.

**If you run two, run 1 and 2.** They share a country, a regulator-facing legal function, an M-Pesa integration, and a customer base of small Kenyan businesses. Modules 3 and 4 share nothing with them but your attention — and each needs a specialist founder you are not.

### 0.6 Four sentences that carry most of the value

1. **"Safaricom's excess margin is in the billing construct, not the radio."** Every prior Kenyan attempt attacked the radio — the licensed, capital-intensive part Safaricom is genuinely good at. The margin lives in expiry forfeiture, denomination laddering and out-of-bundle penalty rates, which are protected by nothing except the absence of an alternative. → [§1.3.3](01-kazimesh.md#133-the-consumer-facing-arbitrage-price-against-the-effective-rate)
2. **"An ATI request that gets refused produces a cause of action; a blocked scraper produces nothing."** Industrialized Access to Information filing is simultaneously a legal data-acquisition engine and an enforcement lever. → [§2.1.3](02-sheriadata.md#213-the-ati-request-engine--data-acquisition-as-enforcement)
3. **"420–480 Wh/kg at under 2 MPa stack pressure."** The second half is the invention. Incumbent solid-state cells need 5–50 MPa; the fixture mass destroys the energy gain. **It is the reason solid-state hasn't shipped, and it was absent from your specification entirely** — while four impossible claims were present. → [§3.1.3](03-solid-state-battery-ip.md#313-performance-targets--what-is-actually-being-claimed)
4. **"Waste heat is measurable from orbit, so a country's real industrial output is measurable without its consent."** The physics has been sound for decades; the thermal sensors to do it daily arrived in the last twenty-four months. That gap is the window. → [§4.1](04-global-macro-engine.md#41-the-genuine-gap-thermal)

### 0.7 The two strategic inversions

**Module 3: stop aiming at Musk.** Tesla open-sourced its patents in 2014, SpaceX refuses to patent, and the ecosystem's entire acquisition history — Maxwell, Hibar, Grohmann, Perbix, SilLion, Swarm — is manufacturing capability and teams, never IP portfolios. You cannot force him to buy anything.

> **The asset that sets your price is not the patent. It is your BATNA.**

Sell to eVTOL first, where 420 Wh/kg is existential rather than merely nice; take defence next; approach automotive last, **and approach all of them.** Six credible bidders is the asset. And within Tesla, the real customer is **Optimus**, not the car — humanoid runtime is specific-energy-gated in a way vehicle range is not.

**Module 4: stop selling alpha to hedge funds as the foundation.** Satellite alt-data alpha decays by construction: you grow revenue by adding clients, and each client you add prices the signal out of existence. Orbital Insight invented the category and was absorbed into Privateer in 2024. **Anchor instead on EU CBAM compliance verification** — legally mandated, non-decaying, growing with the mandate's scope, and running on the identical thermal engine. Then sell the alpha on **priced exclusivity windows**: charge for the decay you are agreeing not to cause. → [§4.4.1](04-global-macro-engine.md#441-the-regulatory-anchor-cbam)

---

## Confidence tagging convention

- **[Certain]** — hard public evidence: a regulator filing, an audited report, a published measurement, a physical constant, a mathematical identity.
- **[Likely]** — strong inference, or a figure triangulated from multiple sources with a stated band.
- **[Guessing]** — filling a gap. Treated as a hypothesis with a named test.

Where a number drives a decision, the derivation is shown so you can re-run it on your own inputs. Where a number is soft, the sensitivity is stated.

**Knowledge boundary:** information is current to approximately mid-2026. Tariffs, licence fees and tiers, tax rates, IMF programme status, satellite constellation capability, and Kenya's virtual-asset and 6 GHz/V-band spectrum frameworks all change fast. **Every price, rate, licence tier and regulatory status in Modules 1 and 2 must be re-verified** against the Communications Authority of Kenya, the Kenya Revenue Authority, the ODPC and current Safaricom results before capital is committed. **The mechanics are durable; the numbers are perishable.**

## Hard gates — verify before spending

| Gate | Module | Why it blocks |
|---|---|---|
| **60 GHz (57–66 GHz) licence-exemption status in Kenya, in writing from CA** | 1 | Do not order any V-band hardware before this returns. If it fails, the Pipeline spine falls back to 70/80 GHz E-band or 5 GHz. |
| **Passpoint profile install success ≥85% across top-5 handset OEMs** | 1 | The entire retention thesis. If it fails you are back to captive portals, which is the model that has never worked. |
| **Commercial thermal constellation resolution, revisit and reliability, under contract** | 4 | Everything downstream assumes a thermal supply that is still being deployed. Verify before training a single model. |
| **Phase 2 symmetric-cell CCD >8 mA/cm² at <3 MPa** | 3 | Answers the Monroe–Newman question. If the compliant anolyte fails here, the invention reduces to an incrementally better electrolyte in a field full of them. Kill the programme. |
| **Kenyan securities/VASP counsel, if any token is contemplated** | 1 | Recommendation is to avoid entirely for v1. |
| **Records Custodian appointed before first ingestion** | 2 | s.106B requires a signatory with genuine system responsibility. No custodian, no admissible evidence, ever. |
