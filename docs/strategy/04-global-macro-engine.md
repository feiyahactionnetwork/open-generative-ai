# Global Macro Engine — Planet-Scale Physical Economic Measurement

**Role:** Satellite Remote Sensing Engineer / Spatio-Temporal AI Architect / Quant Macro Strategist
**Status:** Buildable. **But the market you named is the most crowded one in geospatial, and the business model you named decays by construction.** The differentiated asset is thermal, and the non-decaying buyer is regulatory, not a hedge fund.

---

## 4.0 The two things that will sink this if unaddressed

### 4.0.1 SAR vessel and container counting is a solved, owned, commoditized market. Do not enter it.

[Certain] The specific capability named first in the brief — SAR for container and vessel volume — is the most thoroughly commercialized application in the entire satellite analytics industry:

| Incumbent | Coverage |
|---|---|
| **Kpler** (absorbed MarineTraffic, ClipperData) | Vessel tracking, commodity flows, port activity — the category leader |
| **Vortexa** | Seaborne energy flows |
| **Windward** | Maritime risk, dark-fleet detection, sanctions |
| **Ursa Space Systems** | SAR-derived oil storage and port analytics, sold as a subscription |
| **Kayrros** | Oil inventories, industrial activity, emissions |
| **Spire, ICEYE, Capella, Umbra, Synspective** | The SAR and AIS supply underneath all of the above |
| **Orbital Insight** | The pioneer of oil-tank-float analytics — **absorbed into Privateer in 2024** |

**Read that last line as the market's verdict.** Orbital Insight essentially invented commercial satellite alt-data and did not survive as an independent company. The reason is §4.0.2.

**Strategic instruction: buy vessel and port data from Kpler as an input. Do not rebuild it. Every month spent rebuilding commoditized SAR ship counting is a month not spent on the thing nobody else has.**

### 4.0.2 Alpha decay is not a risk to the model — it is the model

[Certain] Satellite alt-data alpha decays through a well-documented sequence: a novel signal generates excess returns; the vendor sells it to more clients to grow revenue; the signal becomes consensus; it enters the price; the excess return goes to zero; the client cancels. The vendor's growth incentive and the client's alpha are directly opposed.

**The oil-tank-float signal is the canonical example.** In roughly 2015–2017 it was genuinely valuable. Today at least four vendors sell it and it is priced in.

**Therefore the business cannot be "sell alpha to hedge funds" as its foundation.** It can include that, but the foundation must be buyers whose willingness to pay does **not** decay when the vendor adds clients:

| Buyer | What they pay for | Decays with client count? |
|---|---|---|
| Hedge fund | Excess return | **Yes — catastrophically** |
| **Regulated compliance** (§4.4) | **Legally required measurement** | **No — grows with the mandate** |
| DFI / multilateral | Defensible allocation decisions | No |
| Insurer / reinsurer | Peril pricing | No |
| Credit rating / EM credit desk | Defensible risk assessment | Weakly |
| Corporate (procurement, competitive intel) | Operational decisions | No |

**Design the company around the non-decaying buyers, and sell the decaying signal at a premium on exclusivity terms.**

---

## 4.1 The genuine gap: thermal

If everything else is commoditized, what isn't?

[Certain] **High-revisit thermal infrared at useful spatial resolution did not commercially exist until approximately 2024–2026.** The free public thermal record is unusable for economic monitoring:

| Thermal source | Resolution | Revisit | Usable for daily facility monitoring? |
|---|---|---|---|
| Landsat 8/9 TIRS | 100 m (resampled 30 m) | 16 days per satellite, ~8 days combined | **No** |
| MODIS | 1 km | Twice daily | No — too coarse |
| **VIIRS Nightfire** (M10/M11 SWIR) | 375–750 m | **Nightly, global** | **Partially — and it is badly underused** |
| ECOSTRESS (ISS) | ~70 m | Irregular, ISS precessing orbit | Partially |
| **Satellite Vu / HotSat, OroraTech, constellr, Hydrosat** | **3.5–50 m** | **Building toward sub-daily** | **Yes — this is the new capability** |

> **The arbitrage: the physics of inferring industrial production from waste heat has been sound for decades. The data to do it operationally arrived in the last twenty-four months. That is the window.**

**The first law does the work.** Industrial production is an energy conversion process, and the second law guarantees that a large, stable, process-specific fraction of the input energy leaves as waste heat. Waste heat is radiated. Radiation is measurable from orbit. Therefore:

> **You can measure a nation's real industrial output without any cooperation from its statistical agency.**

Energy intensities are well characterized and stable [Certain]:

| Process | Energy intensity | Why it is a good target |
|---|---|---|
| **Primary aluminium smelting** | **13–15 MWh/tonne** | **The best first target in the world.** Enormously energy-intensive, runs continuously (potlines cannot be shut down without destroying them), thermally enormous and distinct, and aluminium is a liquid traded market |
| Cement clinker | 3.0–3.5 GJ/tonne | Kilns are large, hot, and unmistakable |
| EAF steel | 400–500 kWh/tonne | Batch process — arc events are thermally spiky and countable |
| Refinery CDU | Process-specific | Flare signature via VIIRS Nightfire is diagnostic |
| **Data centres** | 100% of draw → heat | **The novel one.** AI capex is the dominant macro story of the decade and datacentre thermal signatures are directly observable. Almost nobody is doing this. |

---

## 4.2 The four applications

Each excludes the prohibited categories: no facial recognition, no traffic management, no targeting, no crop monitoring, no conventional supply-chain tracking.

### 4.2.1 Industrial Thermodynamic Accounting — "physical GDP"

**What it is:** a facility-level, continuously updated estimate of real physical output for every energy-intensive industrial installation on Earth, derived from radiated waste heat and combustion signatures, independent of any national statistical agency.

**Inputs:**

| Sensor | Role |
|---|---|
| **Commercial TIR** (Satellite Vu/HotSat, OroraTech, constellr, Hydrosat) | Primary: facility-level radiant temperature at 3.5–50 m |
| **VIIRS Nightfire** (M10 1.6 µm, M11 2.25 µm SWIR) | Nightly global combustion detection; Planck fitting yields source temperature and radiant heat — **free, operational, underused** |
| Landsat TIRS / ECOSTRESS | Long historical baseline for calibration and trend anchoring |
| Sentinel-1 SAR | Structural change, all-weather; new construction; stockpile volume via interferometric coherence |
| Sentinel-2 / Planet optical | Context, stockpile extent, plume detection |
| **Hyperspectral** (EnMAP, PRISMA, EMIT, Tanager) | Process-gas and mineralogical discrimination; distinguishing what is being made |
| GHGSat, Carbon Mapper/Tanager, Sentinel-5P TROPOMI | Point-source emissions cross-check. **Note: MethaneSAT was lost in mid-2025 — do not architect around it.** |
| VIIRS DNB nighttime lights | Facility activity proxy; shift patterns |

**The physics chain, made explicit because it is the product:**

1. At-sensor radiance → atmospheric correction (MODTRAN/6S with reanalysis profiles) → surface-leaving radiance
2. Temperature–emissivity separation. [Certain] This is fundamentally underdetermined — $N$ bands give $N$ measurements for $N+1$ unknowns. Resolved by split-window methods, or, for industrial targets, by a **known material emissivity prior** (refractory, steel, concrete have well-characterized emissivities), which is the significant advantage of monitoring industrial facilities rather than natural surfaces.
3. Stefan–Boltzmann: $M = \varepsilon \sigma T^4$ → integrate over the facility footprint → **total radiant power** $P_{\text{rad}}$
4. Add convective and conductive losses via a facility-geometry model → **total waste heat** $Q_w$
5. Invert through a process-specific waste-heat fraction $\phi$ → **energy input** $E_{\text{in}} = Q_w / \phi$
6. Divide by process energy intensity → **production rate**, with propagated uncertainty

**Architecture:**

```
Per-modality encoders (frozen after self-supervised pretraining)
 ├─ TIR encoder      ─┐
 ├─ SAR encoder      ─┤
 ├─ Optical encoder  ─┼──► CROSS-ATTENTION FUSION ──► facility token
 └─ HSI encoder      ─┘    (modality dropout in training)
                                       │
                          ┌────────────▼────────────┐
                          │ Continuous-time temporal│
                          │ attention (mTAND-style  │
                          │ learned time embeddings)│
                          │ + explicit missing mask │
                          └────────────┬────────────┘
                                       ▼
                          ┌─────────────────────────┐
                          │ PHYSICS HEAD            │
                          │ regress RADIANT FLUX,   │
                          │ not production.         │
                          │ Invert through explicit │
                          │ thermodynamic model.    │
                          └────────────┬────────────┘
                                       ▼
                     Production estimate + calibrated interval
                                       ▼
                     GNN over facility→firm→commodity→sovereign
```

**Why regress flux rather than production directly** — this is the most important architectural decision in the whole system:

1. **Flux is physically verifiable.** You can validate it against ground truth. "Production" inferred end-to-end from pixels cannot be audited.
2. **Sample efficiency.** The physics does the heavy lifting; the network only has to solve the perception problem.
3. **Interpretability.** When a client asks why the estimate moved, you can answer.
4. **Uncertainty propagates honestly** through an explicit model rather than being invented by a softmax.

**Who is doing it:** SpaceKnow (Satellite Manufacturing Index), Kayrros (industrial activity, emissions), Planet's Planetary Variables, and academic work on nighttime-lights GDP proxies. **Nobody is doing calibrated, facility-level, physics-inverted production estimation with uncertainty intervals at global scale.**

**Why it hasn't been unlocked:** [Likely] **the thermal data gap, primarily.** It was not a missing algorithm and it was not a missing idea — the idea appears in the remote sensing literature repeatedly. It was that until ~2024 no commercial thermal constellation delivered the resolution and revisit required, and Landsat's 100 m / 16-day product cannot see a shift change. Secondarily: the temperature–emissivity separation problem deterred teams without remote-sensing physicists, and the field has been dominated by computer-vision engineers who treat satellite imagery as photographs.

### 4.2.2 Subsurface Hydrological Depletion and its Fiscal Transmission

**What it is:** continuous measurement of groundwater depletion and irreversible aquifer compaction, and its propagation into agricultural output, municipal finance, and sovereign risk. This is hydrogeology, not crop monitoring.

**Inputs:**

| Sensor | Role |
|---|---|
| **Sentinel-1 InSAR** (free, 6–12 day) | **Millimetre-scale land subsidence** — the primary observable |
| ICEYE / Capella / Umbra SAR | High-revisit targeted InSAR on hotspots |
| **GRACE-FO gravimetry** | Total water storage anomaly. ~300 km resolution — **coarse, and that is precisely its value: it is an integral constraint** |
| SMAP (L-band) | Surface soil moisture |
| ECOSTRESS / Landsat TIRS | Evapotranspiration |
| GPM IMERG | Precipitation |
| Sentinel-2 | Irrigated extent |

**The architecture — a Physics-Informed Neural Network on Biot poroelasticity:**

The scientific problem is that InSAR gives you a high-resolution *surface deformation* field, and what you want is the *subsurface head change*. That inversion is ill-posed without well data.

**The resolution: GRACE-FO provides a coarse integral constraint that regularizes the fine-scale InSAR inversion.** Embed Terzaghi/Biot consolidation as a residual loss:

$$\Delta \sigma' = -\Delta u, \qquad \Delta b = -\sum_k S_{k}\, \Delta h_k$$

where $\Delta \sigma'$ is effective stress change, $\Delta u$ pore pressure change, $\Delta b$ vertical displacement, $S_k$ the skeletal storage coefficient of layer $k$, and $\Delta h_k$ the head change. The network learns the spatially-varying storage and compressibility fields subject to:

- **the PDE residual** (poroelastic consolidation),
- **the InSAR data term** at fine spatial scale,
- **the GRACE mass-balance constraint** integrated over the basin,
- **a hysteresis constraint** distinguishing elastic (recoverable) from inelastic (permanent) compaction — this is the economically decisive distinction, because inelastic compaction means the aquifer's storage capacity is **permanently destroyed**.

**Who is doing it:** [Likely] NASA JPL's ARIA products, academic InSAR-subsidence groups, and commercial soil-moisture providers. **The specific fusion — GRACE as integral regularizer on an InSAR-driven poroelastic PINN — is not commercially productized.**

**Why it hasn't been unlocked:** **a missing algorithm, genuinely.** The inversion is ill-posed and the field has treated GRACE and InSAR as separate products serving separate communities. Nobody has coupled them through the physics that actually relates them.

**Buyers:** agricultural lenders, farmland funds, municipal bond insurers, sovereign wealth funds, and — the largest and most mispriced — **building subsidence insurance** in the UK, France, Netherlands, Mexico, and California. Regulatory tailwind: California's Sustainable Groundwater Management Act created a statutory compliance obligation with no adequate measurement layer beneath it.

### 4.2.3 Electrical Grid Fragility and Macroeconomic Transmission

**What it is:** forward-looking assessment of grid stress and cascading-failure risk, and its propagation into industrial output, currency stress, and sovereign credit. Power is the substrate of everything else; grid failure is an economic event before it is an engineering one.

**Inputs:**

| Sensor | Role |
|---|---|
| **RF geolocation** (HawkEye 360, Unseenlabs, Spire, Kleos) | HV transmission corona discharge; SCADA telemetry emissions; substation RF signatures |
| **VIIRS DNB** nighttime lights (nightly) | Outage detection, load-shedding patterns, sub-regional demand |
| Commercial TIR | **Transformer and substation thermal loading** — direct observation of stress |
| Sentinel-1 InSAR | Pylon and dam deformation; structural risk to generation assets |
| Global broadband seismic network | Large rotating machinery signatures; dam and reservoir state |
| **DAS on existing telecom fibre** | **Distributed Acoustic Sensing turns every fibre route into a dense seismic/acoustic array.** The most under-exploited sensing modality on Earth. |
| GPM, ERA5 reanalysis | Hydro inflows, demand-driving temperature |

**Architecture:** a spatio-temporal GNN over the physical grid topology (nodes = substations/generation/load centres; edges = transmission lines), with a **power-flow-informed message-passing scheme** — propagate estimated loading using DC power-flow approximations as an inductive bias rather than learning topology from scratch. Cascading-failure simulation over the learned graph produces a failure-probability surface.

**Who is doing it:** HawkEye 360 sells RF geolocation; the World Bank and others have done nighttime-lights electrification analysis; utilities do their own SCADA analytics internally. **Nobody owns the fusion layer.**

**Why it hasn't been unlocked:** **data isolation, primarily.** DAS data belongs to telecom operators who will not share it; RF constellation data is export-controlled and access-restricted; utility SCADA is confidential. The technical problem is tractable; the **data-access problem is the business**, and solving it is a partnerships-and-legal exercise rather than an engineering one. Price the company accordingly.

### 4.2.4 Parallel-Economy and Capital-Flight Detection

**What it is:** measurement of economic activity that deliberately evades official measurement — informal economies, sanctioned trade, unrecorded capital formation, and the physical footprint of capital flight.

**Inputs:**

| Sensor | Role |
|---|---|
| **VIIRS DNB micro-dynamics** | Not aggregate brightness — **the spatial and temporal texture.** New lit area, intensity distribution shifts, weekday/weekend differentials |
| **Sentinel-1 interferometric coherence** | **Coherence loss detects construction and demolition through cloud.** Construction starts are the single best physical proxy for unrecorded capital formation |
| Planet daily optical | Construction progress, vehicle density at informal markets |
| Commercial TIR | Occupancy of newly built stock — **a building that is built but never heated or cooled is capital flight, not housing.** This distinction is directly observable and it is the most novel signal in this application. |
| RF geolocation | Activity at unofficial border crossings and informal ports |
| AIS gaps + SAR | Dark vessels — cross-check only; Windward owns this |

**The signature that matters:** newly-constructed residential stock showing structural completion in SAR but no thermal occupancy signature is **store-of-value construction** — capital parked in concrete to escape a currency or a tax authority. At scale it is a leading indicator of currency stress, and it is invisible in official statistics by construction.

**Who is doing it:** academic nighttime-lights GDP literature (Henderson, Storeygard & Weil and successors); the World Bank's lights-based work. **Commercially, essentially nobody, because the buyers are EM macro desks and multilaterals rather than the commodity desks the industry has chased.**

**Why it hasn't been unlocked:** **lack of domain imagination.** Every input is free and has been for a decade. The signal requires an economist to specify and a remote-sensing engineer to extract, and those two people rarely work in the same building.

---

## 4.3 The machine learning stack — and the constraint that actually binds

### 4.3.1 Labels, not architecture

**The binding constraint in global satellite ML is label scarcity, not model capacity.** You have petabytes of unlabeled imagery and a few thousand verified facility-production data points. No amount of transformer scale fixes that.

**Therefore, in priority order:**

1. **Self-supervised pretraining is the highest-leverage decision in the entire ML programme.** Masked autoencoding over multi-temporal, multi-sensor stacks — the SatMAE / Prithvi-EO / Clay / Presto / DOFA family. Pretrain on unlabeled global data; fine-tune on the few hundred labeled facilities you can obtain.
2. **Physics-informed heads** (§4.2.1) reduce the label requirement by an order of magnitude, because the network only has to solve perception, not econometrics.
3. **Weak supervision from published aggregates.** National production statistics, company annual reports, customs data, and trade associations give you *aggregate* labels. Train facility-level models under an aggregate consistency constraint — the facility estimates must sum to the published national total where that total is trustworthy. This is a multiple-instance learning formulation and it converts untrustworthy data into useful supervision.
4. **Only then** worry about architecture.

### 4.3.2 The asynchronous-sampling problem, handled correctly

Satellite observations arrive at irregular intervals, with cloud gaps, orbital drift, and per-sensor cadences that never align. **Standard transformers assume regular sampling and will silently misinterpret gaps.**

- **Use continuous-time attention with learned time embeddings** (mTAND-style) or neural controlled differential equations. Time is a continuous covariate, not a sequence position.
- **Represent missingness explicitly** as a mask token with its own embedding. **Never impute a gap as zero or as the previous value** — zero is a valid radiance and forward-fill fabricates stability that is not there.
- **Modality dropout during training.** In production some sensor is always missing. A model that has never trained without its thermal channel will fail the first cloudy week.

### 4.3.3 Calibrated uncertainty is a product requirement, not a nicety

**A macro product without calibrated uncertainty is unsellable to a risk desk, and rightly so.** A point estimate of Chinese aluminium output with no interval is not a number anyone can act on.

- **Conformal prediction** for distribution-free coverage guarantees — the right tool, and underused in geospatial.
- **Deep ensembles** for epistemic uncertainty.
- **Explicit propagation of physical uncertainty** through the thermodynamic inversion: emissivity uncertainty, atmospheric correction residual, waste-heat fraction prior.
- **Publish backtested calibration.** Your coverage claims must be verifiable by the client, or the first sophisticated buyer will test them and find out.

---

## 4.4 Commercialization — and the non-obvious anchor

### 4.4.1 The regulatory anchor: CBAM

This is the most commercially important insight in the module, and it is the one that makes the whole business non-decaying.

[Certain — verify current implementation detail] The **EU Carbon Border Adjustment Mechanism** requires importers to report, and ultimately surrender certificates for, the **embedded emissions** of imported **steel, aluminium, cement, fertilizers, hydrogen and electricity**. Where actual facility-level emissions data cannot be verified, default values apply — and defaults are set conservatively, meaning they are expensive for the importer.

> **An independent, satellite-derived, facility-level energy-and-emissions estimate for non-EU steel, aluminium and cement plants is a regulatorily-mandated data product — and it runs on exactly the same thermal accounting engine as the macro signal.**

Why this changes the business:

1. **The demand is created by law, not by alpha.** It does not decay when you add clients — it *grows* with the mandate's scope.
2. **The buyer is an importer or a verifier with a compliance budget**, not a portfolio manager with a performance test.
3. **Every facility you characterize for CBAM is a facility characterized for the macro product.** One engineering effort, two revenue streams, with the compliance stream funding the R&D that produces the alpha stream.
4. **It is defensible.** A compliance product requires auditability, uncertainty quantification, and methodological transparency — precisely the properties the physics-informed architecture gives you and that an end-to-end black box cannot.

Adjacent mandated demand: TNFD nature-related disclosure, CSRD/CSDDD supply-chain reporting, SGMA groundwater compliance (§4.2.2), and multilateral climate-finance verification.

### 4.4.2 Pricing

[Likely — verify against current market]

| Segment | Product | Price | Notes |
|---|---|---|---|
| **Compliance / verification** | Facility-level embedded-emissions dataset + audit methodology | **$150k–800k/yr** per importer or verifier | **Anchor. Non-decaying. Highest margin.** |
| **Multilateral / DFI** | Sovereign physical-risk platform | **$250k–2M** multi-year | Long cycles, very sticky, reference value |
| **Insurance / reinsurance** | Subsidence and grid-failure peril layers | $100k–600k/yr | Non-decaying |
| **EM credit / sovereign desks** | Physical-activity and stress indicators | $150k–700k/yr | Weakly decaying |
| **Hedge fund — non-exclusive** | Commodity and industrial output signals | $200k–900k/yr | **Decays. Price it as a declining annuity and say so internally.** |
| **Hedge fund — exclusive window** | 30–90 day exclusivity on a named signal | **$1.5–5M/yr** | **The correct way to sell alpha: charge for the decay you are agreeing not to cause** |
| **API / self-serve** | Metered facility queries | $2k–15k/mo | Lead generation, not a business |

### 4.4.3 Build sequence

| Phase | Months | Spend | Deliverable |
|---|---|---|---|
| **0 — Narrow and prove** | 0–6 | ~$900k | **Global primary aluminium smelters only** (~250 facilities). VIIRS Nightfire + Landsat/ECOSTRESS baseline + one commercial TIR contract. Physics inversion. **Backtest against published national production and company reports.** Target: <8% MAPE at monthly facility resolution with calibrated intervals. |
| **1 — Second commodity + first revenue** | 6–12 | ~$1.5M | Add cement clinker. **First CBAM verification pilot** with an EU importer or verifier. First DFI conversation. |
| **2 — Breadth + the macro layer** | 12–20 | ~$3M | EAF steel, refineries, **data centres**. GNN propagation to firm and sovereign level. Launch the sovereign risk product. |
| **3 — Second application** | 18–30 | ~$3.5M | Groundwater PINN (§4.2.2) — the second-strongest application, and entirely on free data |
| **4 — Fusion** | 30+ | — | Grid fragility and parallel-economy layers; cross-application sovereign composite |

**Why aluminium first, specifically:** highest energy intensity of any major industrial process (13–15 MWh/t), continuous operation (potlines cannot be idled without destroying them, so the thermal signal is stable and interruptions are unambiguous and meaningful), physically large and thermally distinct facilities, a globally traded liquid market for validation, **and it is a CBAM-covered good.** It is the single best-conditioned problem in the entire domain, and succeeding on it proves the physics chain end to end before you spend money on harder targets.

---

## 4.5 Winning Execution Strategy

**Build a physics-inverted, facility-level industrial thermodynamic accounting engine — starting with global aluminium — and anchor its revenue on CBAM compliance verification rather than on hedge-fund alpha.**

The decisions that make it work, each against a specific failure mode:

| Decision | Failure it avoids |
|---|---|
| **Do not rebuild SAR vessel/container analytics; license from Kpler** | Entering the industry's most commoditized segment against four entrenched incumbents |
| **Thermal as the differentiated modality** | Competing where everyone already has the same Planet and Sentinel feeds |
| **CBAM compliance as the anchor customer** | **Alpha decay — the mechanism that absorbed Orbital Insight** |
| **Sell hedge-fund alpha only on priced exclusivity windows** | Destroying your own signal to grow revenue |
| **Regress radiant flux, invert through explicit thermodynamics** | An unauditable black box that no compliance buyer or risk desk can accept |
| **Self-supervised pretraining + weak supervision from published aggregates** | The label-scarcity wall that stops most satellite ML programmes |
| **Continuous-time attention, explicit missingness, modality dropout** | Silent failure the first cloudy fortnight |
| **Conformal prediction, published backtested calibration** | Being untestable — and then being tested |
| **Aluminium first, one commodity, proven end to end** | Broad shallow coverage that convinces nobody |

**The single sentence the business rests on:**

> **The second law of thermodynamics guarantees that industrial production radiates waste heat. Waste heat is measurable from orbit. Therefore a country's real industrial output is measurable without its consent — and as of 2024–2026, for the first time, the thermal sensors exist to do it daily.**

**What genuinely threatens this:** the commercial thermal constellations are young, and their delivered resolution, revisit and reliability must be verified under contract before the business plan depends on them. **Make that verification Phase 0's first action, before any model is trained.** Everything downstream assumes a thermal data supply that is, as of now, still being deployed.
