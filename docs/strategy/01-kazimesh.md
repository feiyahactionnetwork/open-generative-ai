# KaziMesh — Decentralized Transport Layer for Nairobi's Dense Informal Economy

**Role:** Principal Network Architect / DePIN Protocol Designer / Telecom Quant
**Status:** Executable. Highest-confidence module in this briefing.
**Corrected headline:** 12-month realistic defection target is **12–18 TB/day**, not 50 TB/day. 50 TB/day is an 18–26 month number. Reasoning in §1.6.

---

## 1.0 Four corrections before any capital moves

These are stated first because each one changes the hardware order, and the hardware order is the largest single cheque in year one.

### 1.0.1 Wi-Fi 7 is the wrong radio. It buys nothing and costs ~2.5×.

[Certain] Wi-Fi 7's advantages — 320 MHz channels, 4096-QAM, Multi-Link Operation — require a **client** that implements them. The device population in Pipeline and Kibera is dominated by sub-$120 Android handsets: predominantly Wi-Fi 5 (802.11ac 1×1), with Wi-Fi 4 still common on the sub-$60 tier and Wi-Fi 6 appearing only on 2023+ devices above ~$150. A Wi-Fi 7 AP serving a Wi-Fi 5 1×1 client delivers Wi-Fi 5 1×1 performance.

Worse, 320 MHz channels **do not exist in the 5 GHz band** — they require 6 GHz, and 6 GHz client radios are effectively absent from the sub-$150 handset market and 6 GHz licence-exemption in Kenya must be independently verified (§1.5).

[Likely] ODM BOM comparison at 5,000-unit volume, outdoor IP66, dual-band, PoE:

| Class | Silicon | BOM | Useful to this client base? |
|---|---|---|---|
| Wi-Fi 5 2×2 outdoor | QCA9563 + QCA9886 | $28–36 | Yes |
| **Wi-Fi 6 2×2 outdoor** | **MT7981B + MT7976C, or IPQ5018** | **$38–55** | **Yes — OFDMA + TWT are the real wins** |
| Wi-Fi 6E 2×2 | + 6 GHz front-end | $70–95 | No — no clients |
| Wi-Fi 7 2×2/4×4 | MT7990 / IPQ9574 | $95–160 | No |

**Decision: Wi-Fi 6, 2×2, 2.4 + 5 GHz.** The feature that actually matters in a 150-device cell is **OFDMA uplink scheduling and BSS colouring**, both Wi-Fi 6, both effective even when clients are legacy (the AP schedules around them). Target Wi-Fi 6 clients as they arrive; do not pay for Wi-Fi 7 to serve Wi-Fi 4.

**Your $65 BOM target is achievable — at Wi-Fi 6, without solar, without NVMe, on the leaf node class only.** It is not achievable for a Wi-Fi 7 + 60 GHz + solar + NVMe node; that device is $600–1,100. The resolution is node **tiering** (§1.1), not cost-engineering a single impossible device.

### 1.0.2 Pipeline and Kibera are opposite RF problems. Do Pipeline first.

This is the most consequential correction in the module and it is geographic, not technical.

- **Pipeline (Embakasi)** — [Certain] one of the densest residential districts in Africa: 6–12 storey reinforced-concrete blocks, ~100k+ residents in roughly 1 km², formal-ish rental tenancy, mains grid power, working-class tenants with steady if small cash income. RF topology is **vertical**: a small number of rooftop anchors with clean line-of-sight across a flat roofscape, then in-building distribution down a riser. This is close to an ideal 60 GHz environment and an ideal **landlord-channel** environment.
- **Kibera** — low-rise corrugated-iron, irregular roof heights, constant informal reconstruction, dense foliage in parts, higher hardware-theft exposure, more volatile cash income. RF topology is **horizontal**: 60 GHz line-of-sight is repeatedly broken by roof modification; the correct spine is 5 GHz PtMP from a few tall fixed anchors (water towers, church and school buildings, mast sites).

Treating them as one deployment is how you buy the wrong radios twice.

**Sequence: Pipeline → Mukuru/Kayole/Umoja (similar vertical stock) → Kibera.** Pipeline gives you the highest revenue per node, the lowest CAC (landlord channel), the lowest theft rate, and a clean 60 GHz proving ground. Kibera is the harder, lower-ARPU, higher-ops problem and belongs after the playbook is stable.

### 1.0.3 Ad-funded free data is arithmetically dead. The numbers, not the opinion:

[Certain] This is precisely what killed BRCK's Moja (free ad-supported public Wi-Fi on Nairobi matatus and buses, wound down ~2021–22).

Derivation:
- Kenyan programmatic display/video CPM: [Likely] **$0.15–0.60** net to publisher.
- An interstitial cadence tolerable to users: one per ~20–30 minutes of session, plus one at association. Call it **4–8 impressions per GB consumed** (a GB of mixed social/video is many hours of session).
- Ad revenue per GB = 6 impressions × $0.35/1000 = **$0.0021/GB.**
- Fully-loaded delivery cost per GB (§1.3) = **$0.05–0.09/GB** including amortized capex, ops and host share.

**Ad funding covers 2–4% of cost. It is short by a factor of 25–40.** No CPM optimization closes a 30× gap. Any business plan whose free tier is CPM-funded is dead on contact.

**What does work is CPA, not CPM** — *sponsored access*, where a named counterparty pays for a named outcome:
- Bank / SACCO pays **KSh 300–900 per funded, KYC-complete account** — Kenyan digital-account CPAs are in this band [Likely]. That single conversion funds 6–20 GB.
- Edtech/employer pays per **activated learner** or per **shift-worker connected**.
- Government/NGO pays per **verified service enrolment** (NHIF/SHA, Huduma, county services).

Zero-rating a sponsor's own property costs you near-nothing (sponsor traffic is a small fraction of bytes) and converts at CPA rates two to three orders of magnitude above CPM. **Free tier = zero-rated walled garden funded by CPA. Open internet = paid.** That asymmetry is the entire free-tier business model.

### 1.0.4 Squid + IPFS caching is a 2012 architecture. It will return a ~0–3% hit rate.

[Certain] Over 95% of consumer web traffic is TLS-encrypted, and the dominant applications — YouTube, TikTok, Instagram, WhatsApp, Facebook — use TLS 1.3 with QUIC (UDP/443) and, in several cases, certificate pinning. A transparent Squid proxy cannot decrypt, cannot cache, and attempting MITM interception will break the apps outright and is a Data Protection Act exposure besides. IPFS is irrelevant here: the content users want is not on IPFS, and nothing you do makes it so.

**Replace the entire local-cache thesis with three things that actually work:**

1. **Peer at KIXP** (Kenya Internet Exchange Point, TESPOK-operated, Nairobi). [Certain] KIXP is one of Africa's most mature IXPs with Google, Meta, Akamai, Cloudflare, Microsoft and Netflix present. Peering displaces **[Likely] 55–70% of your traffic** off paid transit for a flat port fee. This is the single highest-ROI infrastructure decision in the build and it costs a few hundred dollars a month.
2. **Host vendor caches once you clear their thresholds** — Google Global Cache (GGC), Netflix Open Connect Appliance, Meta FNA, Akamai AANP. These are supplied free by the vendor to qualifying networks with an ASN. You will not qualify at launch; you will around the 3–8 Gbps peak mark. Design the PoP rack with the space, power and cross-connect for them from day one.
3. **Use the NVMe for content you own** — a zero-rated local library: Kiwix (offline Wikipedia), Kolibri (offline curriculum), a local classifieds/jobs board, sponsor-hosted content, OS and app update mirrors (F-Droid, APK mirrors of sponsor apps). This is not a cache, it is **inventory for the free tier**, and it is the thing that makes a zero-out-of-pocket product feel like a product rather than a nag screen.

Net effect: the NVMe stays in the BOM, at the **PoP**, not on every node. That alone removes $60–90 × 3,000 nodes ≈ **$200k+** of misallocated capex.

---

## 1.1 Hardware and topology schematic

Four node classes. Blending them is what gets you to a defensible average cost per served user.

```
                      [ Nairobi carrier-neutral DC ]
                      iColo NBO1 / ADC / PAIX
                      ├── 2× 10GE transit (diverse carriers)
                      ├── 1× 10GE KIXP peering port  ◄── 55-70% of bytes
                      └── GGC / OCA / FNA cache racks (phase 2)
                                   │
                            dark fibre / leased λ
                                   │
        ┌──────────────────────────▼──────────────────────────┐
        │  POP — Estate Aggregation Node (1 per ~1-2 km²)     │
        │  Router+BNG, L2 switch, FreeRADIUS/AAA, OSU server, │
        │  NVMe content library, 48V DC plant + 4h battery    │
        └───────┬─────────────────────────────┬───────────────┘
                │ 60 GHz V-band spine         │ 5 GHz PtMP spine
                │ (Pipeline: rooftop LOS)     │ (Kibera: anchor masts)
        ┌───────▼────────┐            ┌───────▼────────┐
        │ DN  Distribution│            │ DN  Distribution│
        │ 60 GHz 2-4 sect │◄──mesh──►  │ 5 GHz sector    │
        │ + Wi-Fi 6 AP    │            │ + Wi-Fi 6 AP    │
        └───────┬─────────┘            └───────┬─────────┘
                │ CAT6 riser / short 5 GHz hop  │
        ┌───────▼───────────────────────────────▼─────────┐
        │  CN  Leaf AP  ×8-14 per DN   ($65 target class) │
        │  Wi-Fi 6 2x2, 802.11u/k/v/r, WPA3-Ent, PoE      │
        └─────────────────────────────────────────────────┘
```

### 1.1.1 CN — Client/Leaf Node — **the $65 node**

| Item | Spec | BOM @5k units |
|---|---|---|
| SoC + radio | MediaTek MT7981B + MT7976C (Wi-Fi 6, 2×2 dual-band) or Qualcomm IPQ5018 | $16–22 |
| RAM / flash | 256 MB DDR3 / 128 MB NAND | $4–6 |
| PA/LNA, front-end, filters | | $4–6 |
| Antennas | 2× dual-band 5 dBi omni, or 2× 9 dBi sector for corridor coverage | $3–5 |
| PoE PD (802.3af) | | $2.5–4 |
| Enclosure IP66 + UV-stable ASA | | $5–8 |
| Surge/ESD protection (essential — Nairobi lightning) | gas discharge tube + TVS on Ethernet | $2–3 |
| PCB, assembly, test | | $4–6 |
| **Subtotal** | | **$40.5–60** |
| Mount, CAT6 drop, cable gland, labour | | $12–20 |
| **Installed cost per leaf** | | **$53–80 → plan $65** |

Your $65 target holds. Firmware: **OpenWrt 23.05+ / 24.x** with `hostapd` built for 802.11u/Passpoint, `wpa_supplicant` mesh where needed, and a slim agent for telemetry and remote config. Do not write a custom OS.

### 1.1.2 DN — Distribution Node

**Pipeline / vertical (60 GHz):**

[Certain] 60 GHz V-band physics you must design around:
- Oxygen absorption peaks near 60 GHz at roughly **10–15 dB/km** — irrelevant at 150 m (≈2 dB), fatal at 2 km.
- Rain attenuation: Nairobi's ITU-R rain rate at 0.01% exceedance is [Likely] ~50–60 mm/h. At 60 GHz that is **~18–22 dB/km**. Over a 200 m hop: **~4 dB** — fully absorbable in link budget. Over 800 m: ~16 dB — marginal.
- Beamwidth is 2–3°. **Mount rigidity and wind sway dominate availability**, not the radio.

**Therefore: design the 60 GHz spine for 100–300 m hops with mechanically over-specified mounts.** That matches Pipeline's block spacing almost exactly. It does not match Kibera.

Hardware choice — and here is a real capital saving. Meta's **Terragraph**-certified gear (Cambium cnWave V3000/V5000) is excellent and expensive: [Likely] **$1,200–2,800/node**. **MikroTik's 60 GHz line (wAP 60G AP, Cube 60G, CubeSA 60G)** runs **$100–280/end** with 802.11ad PtP/PtMP and MikroTik's own mesh extensions.

**Recommendation: MikroTik 60 GHz for the estate spine; reserve Terragraph-certified gear for the 2–4 highest-capacity trunk hops carrying aggregate PoP traffic.** Terragraph's TDMA scheduler and IG-based routing genuinely outperform under heavy multi-hop mesh load, but you will not be heavy-multi-hop if you keep hop counts to ≤3 — which you should anyway, because every mesh hop halves airtime.

**The hop-count rule:** each wireless hop on a shared channel roughly halves usable throughput. Three hops ≈ 1/8 capacity. **Architect for ≤2 wireless hops from PoP to leaf**, buying fibre or licensed PtP where that fails. Mesh is a tool for the last 200 m, not a substitute for backhaul. [Certain] Failure to internalize this killed most municipal mesh projects of the 2005–2012 era (Philadelphia Wireless, Tropos-era deployments).

| DN class | Hardware | Cost |
|---|---|---|
| Pipeline spine DN | 2× MikroTik 60 GHz + rigid mount + PoE + surge | $320–560 |
| Pipeline trunk DN (top 10%) | Cambium cnWave V3000 | $1,300–1,900 |
| Kibera anchor DN | 5 GHz 90° sector ×3 (Mimosa C5c / Ubiquiti LTU / Cambium ePMP 4500) | $450–800 |

### 1.1.3 Power — solar belongs on ~12% of sites, not all of them

[Certain] Both Pipeline and Kibera have mains grid presence; Kenya Power's slum-electrification programmes connected large parts of both. The failure mode is **instability and outage**, not absence.

- **Default (≈88% of nodes): host mains + small DC UPS.** A 12 V 20 Ah LiFePO4 + buck/boost + PoE injector rides a 4–8 hour outage for a 10 W AP. Cost **$28–42.** Host reimbursement for power: KSh 250–450/month, folded into their revenue share.
- **Solar (≈12%: orphan relay sites, masts, no willing host):** 100 W panel + 512 Wh LiFePO4 + MPPT + enclosure = **$190–290.**

Deploying the solar assembly universally instead of selectively is a **~$450k error at 3,000 nodes.** Nairobi's ~4.5–5.5 kWh/m²/day insolation makes solar *work*; it does not make it *cheap*.

### 1.1.4 Passpoint / Hotspot 2.0 — the actual moat

This is the part almost no African community-Wi-Fi operator has done, and it is why their retention is poor.

[Certain] The captive portal is the single largest source of user drop-off in public Wi-Fi. Every association requires a browser, a redirect that modern OSes increasingly block, a login, and a session that dies on sleep. Passpoint eliminates all of it: the phone treats the network like a cellular network and joins silently, everywhere in the federation, forever.

**Stack:**

| Layer | Component | Notes |
|---|---|---|
| L2 discovery | **802.11u / ANQP** — advertise NAI realm `kazimesh.co.ke`, roaming consortium OI, venue info | `hostapd` `interworking=1`, `hs20=1` |
| Association | **WPA3-Enterprise** (fall back WPA2-Ent for legacy); **OWE** for the open zero-rated SSID | AES-GCMP-256 where supported |
| Auth | **EAP-TTLS/MSCHAPv2** for v1 (works on the widest device base), migrate to **EAP-TLS** per-device certs for v2 | Per-device credential, never a shared PSK |
| AAA | **FreeRADIUS 3.2+**, MySQL/Postgres backend, CoA/Disconnect-Message for live quota enforcement | RFC 5176 CoA is how you cut off an exhausted user without a portal |
| Provisioning | **OSU (Online Sign-Up) server**, SOAP-XML or OMA-DM | iOS: signed `.mobileconfig`. Android 11+: `PasspointConfiguration` installed by the KaziMesh app or a downloaded `.config` |
| Roaming | **802.11k** (neighbour reports) + **802.11v** (BSS transition) + **802.11r** (fast BSS transition, FT-EAP) | Without these a walking user re-authenticates at every AP; with them, handover is sub-100 ms |
| Accounting | RADIUS Accounting Interim-Update every 60 s → ledger (§1.2) | This is your metering source of truth |

**Onboarding flow (one time, ~40 seconds):**
1. User joins open SSID `KaziMesh-Setup` (OWE).
2. Lands on the OSU page (this is the *only* portal they ever see).
3. Pays or claims a sponsored pass via M-Pesa STK Push.
4. Server issues a per-device credential; profile installs.
5. Device now auto-joins `KaziMesh` on every node, permanently, including after reboot, roaming and long idle.

**Deliberate design choice: EAP-TTLS before EAP-TLS.** EAP-TLS with per-device certificates is cryptographically superior and is the right end state, but certificate installation on low-end Android is fragile across OEM skins. Ship TTLS, measure install success rate by OEM, migrate the devices that tolerate it.

**Compliance gate:** issuing per-device credentials and holding RADIUS accounting records is personal-data processing. [Certain] Registration as a **data controller with Kenya's Office of the Data Protection Commissioner (ODPC)** under the Data Protection Act 2019 is mandatory before launch, and you need a retention schedule and a lawful basis. Budget for a DPIA. This is cheap and non-negotiable.

---

## 1.2 Monetization and the arbitrage engine

### 1.2.1 Revenue lines, ranked by leverage

| # | Line | Mechanism | Take | Why it ranks here |
|---|---|---|---|---|
| **1** | **Landlord/building bundles (Pipeline)** | Sell the landlord whole-building coverage at KSh 200–400/unit/month, collected with rent | 55–70% GM | **Near-zero CAC, near-zero churn, near-zero collections cost.** A 120-unit Pipeline block is one sale, one install, 120 subscribers. This is the highest-leverage channel and it is under-exploited. |
| **2** | **Prepaid consumer passes** | M-Pesa Daraja STK Push; KSh 20/day, KSh 120/week, KSh 500/month per device | 60–75% GM | The default retail product. Priced against Safaricom's *effective* rate, not nominal (§1.3.3). |
| 3 | **Sponsored access (CPA)** | Zero-rated walled garden; sponsor pays per acquisition/activation | 80–90% GM | Funds the free tier. Replaces the dead CPM model. |
| 4 | **Estate Captain franchise** | Host earns 15–25% rev-share + device sales margin | — | This is a *cost* line that behaves like a growth line. See §1.4.2. |
| 5 | **Wholesale / MVNO-style transit** | Sell capacity to other small ISPs, schools, clinics, SACCOs off your PoP | 40–55% GM | Monetizes PoP capacity you have already paid for. |

**Note what is absent: CPM advertising.** Per §1.0.3.

### 1.2.2 Payment rail: M-Pesa Daraja, and the dependency you must name

The rail is **Safaricom's Daraja API** — STK Push (`/mpesa/stkpush/v1/processrequest`), C2B confirmation callbacks, Till or Paybill shortcode. There is no realistic alternative: M-Pesa is where the money is.

**This is a strategic dependency on your competitor, and you must say so to investors before they find it.** Assessment:

- [Likely] **Low near-term risk.** M-Pesa merchant acquiring is a mass-market, arms-length product with tens of thousands of merchants. Safaricom terminating a licensed ISP's Till for competitive reasons would be a clean abuse-of-dominance case before the **Competition Authority of Kenya** and a regulatory embarrassment in a cost-of-living political climate. They are more likely to ignore you below the retaliation threshold (§1.6.3).
- **Mitigations, implemented from day one, not later:** (a) parallel Airtel Money and T-Kash integration even at low volume, so the switch is code-complete; (b) a bank-rail fallback (Equity/KCB direct integration or a PSP aggregator — Cellulant, Kopo Kopo, Flutterwave); (c) **voucher cards sold through the Estate Captain network** — physical scratch cards are unglamorous, unblockable, and work during outages; (d) written legal opinion on file regarding CAK remedies, so a retaliation event triggers a prepared filing, not a scramble.

### 1.2.3 The bandwidth ledger — and the honest verdict on tokenizing it

**What you need functionally:** a tamper-evident, auditable record of (bytes delivered, by which node, to which credential, at what time) that (a) computes Estate Captain revenue shares without disputes, (b) enforces quotas in real time, (c) survives partition when a node is offline.

**Architecture that meets it:**

```
AP (hostapd) ──RADIUS Acct Interim-Update (60s)──► FreeRADIUS
                                                       │
                                            ┌──────────▼──────────┐
                                            │ Metering service    │
                                            │ idempotent by       │
                                            │ (session_id, seq)   │
                                            └──────────┬──────────┘
                    ┌──────────────────────────────────┼─────────────────┐
                    ▼                                  ▼                 ▼
         Postgres double-entry ledger      Quota engine ──CoA──► AP    Merkle
         (append-only, hash-chained)       (RFC 5176 disconnect)      daily root
                    │                                                     │
                    └──── Estate Captain settlement (M-Pesa B2C) ◄────────┘
```

Offline tolerance: APs buffer accounting records locally and replay on reconnect; the metering service is idempotent on `(Acct-Session-Id, Acct-Input-Gigawords/Octets)` so replays cannot double-count. This is the actual hard engineering problem and it has nothing to do with blockchains.

**On the L2 token, directly:**

**I disagree with tokenizing the bandwidth ledger for v1.** Reasoning:

1. **The Helium autopsy.** [Certain] Helium's token rewarded *coverage provision*, not *traffic served*. The result was hundreds of thousands of hotspots deployed in places with no demand, a network whose data-credit revenue was reported at a few tens of thousands of dollars per month against enormous token issuance, and a subsequent collapse in deployment once emissions were cut. **Helium proved that token incentives solve the supply-side problem. KaziMesh does not have a supply-side problem.** Nairobi has no shortage of people willing to host a box on their roof for a revenue share. Your binding constraint is **demand and collections**, and a token makes both worse by adding friction to a user who transacts in KSh over M-Pesa.
2. **Kenyan regulatory exposure.** [Likely] Kenya enacted a virtual-asset-service-provider licensing regime in 2025 bringing VASPs under CBK/CMA supervision, alongside the existing Capital Markets Act. A token that represents a claim on network revenue is a security in most readings. You would be adding a financial-services licensing track to a telecom licensing track, in parallel, at seed stage. **Verify the current VASP framework with Kenyan counsel before any token design work.**
3. **It does not reduce trust requirements where it matters.** The Estate Captain's dispute is "did my node really deliver 400 GB?" — the byte count originates from *your* AP firmware. Putting that number on-chain notarizes a number you produced; it does not make it true. On-chain settlement secures the *transfer*, not the *measurement*. The measurement is the disputed thing.

**What I would do instead:** hash-chain the daily settlement records and publish the **Merkle root** to any cheap public chain (or simply to a transparency log). Estate Captains get a verifiable inclusion proof for their own settlement and can prove tampering after the fact. You get the audit property for ~$5/month and zero licensing exposure.

**If you take token capital anyway** — and there are legitimate reasons to, principally hardware-financing capital formation — then the minimum-risk design is:
- **Base:** Base or Arbitrum (sub-cent gas, EVM, real fiat on-ramps). Avoid a bespoke chain.
- **Token function:** strictly a **hardware-financing instrument** — Captains stake or finance node capex, receive a KSh-denominated revenue share settled in stablecoin, with the token as collateral/governance only. **Never** a bandwidth-payment currency for consumers.
- **Consumers never touch it.** Consumers pay KSh via M-Pesa, full stop.
- **Gate it** behind written Kenyan securities and VASP counsel, before launch, not after.

---

## 1.3 Financial model and unit economics

### 1.3.1 Wholesale transport cost per GB — derivation

**Step 1 — Mbps-to-GB conversion.**
One Mbps sustained for 30 days = 1×10⁶ bit/s × 2.592×10⁶ s = 2.592×10¹² bits = **324 GB** at 100% utilization.

Real networks bill on 95th-percentile and carry a diurnal peak-to-mean ratio of **2.5–3.5×** (informal-settlement traffic is *more* peaked than average — evening concentration after work). At 3.0×:

**Deliverable volume per billed Mbps ≈ 324 / 3.0 = 108 GB/month.**

**Step 2 — Nairobi wholesale input costs.** [Likely], for a small ISP buying 1–10 Gbps:

| Input | Unit cost | Note |
|---|---|---|
| IP transit (Nairobi, carrier-neutral DC) | **$2.50–6.00 /Mbps/mo** | Kenya benefits from Mombasa landings (SEACOM, TEAMS, EASSy, LION2, DARE1, PEACE, 2Africa) + Nairobi DC density. Verify current rates. |
| KIXP peering port | ~$100–300/mo flat | Displaces 55–70% of bytes |
| Metro λ / dark fibre, DC → PoP | $300–900 /mo per 10G route | Liquid, Jamii, Wananchi, or dark fibre IRU |
| Cross-connects, rack, power | $400–1,200/mo | iColo NBO1 / ADC / PAIX |

**Step 3 — blended.** Take transit at $4.00/Mbps/mo. With 62% of bytes peered:

Effective paid-transit fraction = 0.38
Blended upstream = $4.00 × 0.38 = **$1.52/Mbps/mo equivalent**
Per GB = $1.52 / 108 GB = **$0.0141/GB**

Add metro transport, PoP overhead and IP/AS costs amortized over volume: **+$0.006–0.012/GB.**

> ### **Marginal wholesale transport cost: $0.020–0.026 / GB**
> ### **Safaricom retail: $0.80–1.20 / GB (nominal). Effective: $1.10–2.00 / GB after expiry forfeiture (§1.3.3).**
> ### **Raw transport spread: 40–75×**

**This spread is not the business.** It is the headroom. The business is what it costs to *reach* the user, which is node capex, ops, and collections. That is §1.3.2.

### 1.3.2 Node-level P&L — the number that decides everything

**Pipeline leaf node, steady state, month 6+:**

*Fully-loaded capex per leaf:*

| Component | Cost | Allocation |
|---|---|---|
| Leaf AP, installed | $65 | 1:1 |
| DN share | $50 | $400 DN ÷ 8 leaves |
| PoP + spine share | $50 | $5,000 PoP ÷ 100 leaves |
| DC UPS | $32 | 1:1 |
| **Fully-loaded capex / leaf** | **$197** | |

*Monthly operating:*

| Line | Amount | Basis |
|---|---|---|
| **Users per leaf** | 80 | 2×2 Wi-Fi 6 in a dense block: 100–160 associated, 30–60 concurrent. 80 paying is conservative. |
| **GB/user/month** | 6.0 | Unconstrained Wi-Fi consumption runs 3–6× metered mobile consumption. Kenyan mobile ARPU maps to ~1–2 GB/mo; Wi-Fi users settle at 5–10 GB. |
| **Traffic/leaf/month** | **480 GB** | |
| Blended realized ARPU | $3.00 | Mix: landlord-bundle ~$2.00, prepaid monthly ~$3.85, daily-pass users lower and lumpier. |
| **Revenue / leaf / month** | **$240** | 80 × $3.00 |
| — Transport @ $0.023/GB | ($11) | |
| — Power (host reimbursement) | ($3) | |
| — Estate Captain share @ 20% | ($48) | |
| — NOC, field ops, support allocation | ($35) | Loaded, incl. truck rolls and churn-install |
| — Bad debt, fraud, credential sharing | ($10) | ~4% |
| — Payment processing (M-Pesa ~1.5%) | ($4) | |
| **Contribution margin / leaf / month** | **$129** | **54% CM** |
| **Capex payback** | **~1.5 months** | $197 / $129 |

**Fully-loaded delivery cost per GB** = ($11+$3+$48+$35+$10+$4) / 480 GB = **$0.23/GB** at this ARPU — dominated by the Captain share and ops, not by bandwidth. Excluding revenue-linked costs (Captain share, processing), the **structural cost floor is $0.12/GB**. Against Safaricom's $0.80–1.20 nominal, you have a **5–10× structural cost advantage at the fully-loaded level**, which is the durable number. The 40× transport spread is the headline; **$0.12/GB vs $0.80/GB is the defensible one.**

**Stress test — halve everything:** 40 users, $2.00 ARPU → revenue $80, costs ~$45, CM $35/month, payback **5.6 months**. Still strong. The model does not require the optimistic case to work.

**Where it actually breaks:** users per leaf below ~22, or ARPU below ~$1.20, or Captain churn forcing repeated re-installs. **Ops density, not technology, is the failure mode.** Every prior community-Wi-Fi death in the region is an ops-density death.

### 1.3.3 The consumer-facing arbitrage: price against the *effective* rate

[Certain] Safaricom's headline price per GB understates what users actually pay, because the dominant retail construct is a **time-expiring bundle**. Unused data is forfeited at expiry. This is the core margin mechanism and it is not a radio cost — it is a billing construct.

Mechanism decomposition:

1. **Expiry forfeiture (breakage).** A 24-hour bundle converts a volume good into a time-limited option. [Guessing — this is the single most important number to measure and the hardest to obtain; treat as hypothesis] forfeiture on short-dated bundles plausibly runs **15–30% of purchased volume**. Every forfeited GB is 100% gross margin. **Effective price per *consumed* GB is therefore 1.18–1.43× the nominal.**
2. **Denomination laddering.** Price per GB falls steeply with bundle size. The users buying the smallest denominations are liquidity-constrained, not low-value. This is price discrimination on **cash-on-hand**, not willingness to pay — a poverty premium of [Likely] 3–8× between the smallest daily denomination and the largest monthly one.
3. **Out-of-bundle penalty rates.** Browsing without an active bundle is charged at a per-MB rate orders of magnitude above bundle rates. The function of this is not revenue; it is **fear**. It drives defensive over-purchase of bundles that are then forfeited, feeding mechanism (1).
4. **Tax stacking.** [Likely] Excise duty on telecom/data services (15% at last verification, having been 15% → 20% in Finance Act 2021 → back to 15% in Finance Act 2022 — **re-verify current rate**) applies before VAT at 16%. Compound uplift = 1.15 × 1.16 = **1.334**. Roughly **25% of a retail bundle price is tax**, not margin. *This is real and it applies to you identically* — you get no tax arbitrage, and any pitch claiming one is wrong.

**Where the artificial inflation actually sits, ranked:**

| Layer | Nature | Attackable? |
|---|---|---|
| **Expiry forfeiture** | Pure billing construct. Not a cost. | **Yes — this is the attack surface.** Sell data with no expiry. |
| **Denomination laddering** | Extraction on a liquidity constraint | **Yes.** Flat per-GB pricing at every denomination. |
| **Out-of-bundle penalty** | Fear premium | **Yes.** No out-of-bundle concept exists on Wi-Fi. |
| RAN capex + site opex | **Genuinely the dominant real cost.** Towers, power, diesel, leases, backhaul, maintenance. [Likely] KSh 8–20/GB on loaded urban sites, far higher rural. | **No — and do not try.** This is what Safaricom is good at. |
| Spectrum licence amortization | Real but tiny per GB at their volumes [Likely] <KSh 1/GB | No |
| Excise + VAT (~25% of retail) | Statutory | No — applies to you too |
| International capacity | [Certain] Collapsed in cost. Not a meaningful driver since ~2015. | Already gone |
| Corporate overhead + margin | Kenya EBITDA margin [Likely] ~50%+ | Consequence, not cause |

> **The single most important strategic sentence in this module:**
> **Safaricom's excess margin is in the billing construct, not the radio.** Every previous Kenyan disruption attempt attacked the radio — the licensed, capital-intensive, genuinely difficult part. The margin lives in expiry, laddering and penalty rates, which are protected by nothing except the absence of an alternative.

**Therefore the product is defined by what it refuses to do:**

| KaziMesh | Safaricom construct |
|---|---|
| **Data never expires** | 1h / 24h / 7d / 30d expiry |
| **One price per GB at every denomination** | Steep laddering |
| **No out-of-bundle rate — you simply stop** | Penalty per-MB rates |
| **Per-device credential, roams the whole footprint** | Per-SIM |

### 1.3.4 Price points and the "≤$0.15/day or zero" requirement

| Tier | Price | Per GB | vs Safaricom effective |
|---|---|---|---|
| **Zero-rated (free)** | KSh 0 | — | Walled garden: local library, sponsor properties, jobs board, edu content, KaziMesh chat. **CPA-funded, not CPM.** |
| **Daily pass** | KSh 20 (~$0.15) | 1.5 GB, no expiry → **$0.10/GB** | **8–20× cheaper** |
| **Weekly** | KSh 120 (~$0.92) | 12 GB, no expiry → **$0.077/GB** | **10–26× cheaper** |
| **Monthly unlimited (FUP 60 GB)** | KSh 500 (~$3.85) | at 30 GB used → **$0.13/GB** | **6–15× cheaper** |
| **Landlord bundle** | KSh 200–400/unit/mo | building-wide | Lowest CAC channel |

**Your $0.15/day target is met exactly**, and the zero-out-of-pocket tier is met via CPA sponsorship rather than the CPM model that killed Moja.

Note the daily pass at $0.10/GB against a fully-loaded cost of $0.12/GB: **the daily tier is roughly break-even to slightly negative and is an acquisition instrument, not a profit centre.** Margin comes from monthly and landlord tiers. Price the daily tier as customer acquisition and say so internally, or you will misread your own cohort economics.

---

## 1.4 Landscape and failure autopsy

### 1.4.1 What has been tried, and exactly what killed it

| Attempt | What it was | Cause of death | Trap KaziMesh must avoid |
|---|---|---|---|
| **Telkom Kenya / Orange** | State-entangled #3 MNO | Chronic undercapitalization; the Airtel–Telkom merger announced 2019 collapsed by 2021 amid regulatory and state-shareholding complications. A zombie competitor. | Don't be a fourth MNO. Don't need spectrum. |
| **Airtel Kenya** | Priced aggressively, gained subscriber share | [Certain] Gained *subscribers*, not *revenue share*. Beaten by the **agent-distribution network and M-Pesa**, not by radio quality. | **Distribution is the moat, not price.** Hence Estate Captains (§1.4.2). |
| **MVNOs (Finserve/Equitel, Mobile Pay, Zioncell)** | Licensed from 2014 | [Certain] **Safaricom never hosted an MVNO.** All Kenyan MVNOs ride Airtel. Without a *mandated* wholesale rate on the dominant network, an MVNO is a reseller of the weaker network. Only Equitel survived — because Equity Bank had an independent distribution moat. | **Never require a wholesale deal with Safaricom.** KaziMesh doesn't. |
| **CAK dominance remedy** | A 2017 competition study recommended declaring Safaricom dominant with asymmetric remedies (mandated infrastructure sharing, wholesale access, possible M-Pesa separation) | [Likely] Shelved after political pushback. **The regulatory route to cheaper data has been tried and it failed politically.** | **Do not build a plan whose critical path is a regulatory remedy.** Regulatory upside is a call option, never the thesis. |
| **BRCK Moja** | Free ad-funded Wi-Fi on matatus/buses | [Certain] **CPM arithmetic** (§1.0.3). Ad revenue per GB was ~1/30th of delivery cost. | CPA, never CPM. |
| **Google Loon (Telkom Kenya)** | [Certain] Kenya was Loon's first commercial deployment, July 2020; shut down January 2021 | Unit economics. Per-user cost never approached terrestrial. | Capital-efficiency discipline; $197/leaf, not $millions/balloon. |
| **TV White Space (Microsoft 4Afrika + Mawingu, Nanyuki, 2013–)** | Sub-GHz unlicensed broadband | [Certain] **Chipset ecosystem never materialized** — no volume silicon, so no cost curve. Geolocation-database requirement added regulatory drag. The 700 MHz digital dividend was auctioned to MNOs anyway. | **Never depend on silicon that doesn't yet ship in volume.** Wi-Fi 6 and 802.11ad ship by the tens of millions. |
| **Helium / DePIN mobile** | Token-incentivized coverage | [Certain] Rewarded coverage, not traffic. Required CBRS — a **US-specific** shared-spectrum regime with no Kenyan equivalent — and an MNO roaming agreement to be useful. | No token-for-coverage. No dependency on a shared-spectrum regime Kenya doesn't have. |
| **Poa Internet** | Street-level fixed wireless in low-income Nairobi, ~KSh 1,500/mo unlimited; institutionally funded | **Not dead — the strongest operator in this space and the most credible competitor.** Model is capital-heavy per household: a home connection, a home install. | **Do not compete head-on.** Poa sells a *household* connection. KaziMesh sells a *device* pass to the majority who will never buy a household connection. See below. |
| **Municipal mesh (Philadelphia, Tropos era, 2005–12)** | City-scale Wi-Fi mesh | [Certain] Multi-hop capacity collapse (each hop ≈ halves throughput), plus no backhaul economics. | **≤2 wireless hops.** §1.1.2. |

### 1.4.2 The open wedge, stated precisely

Poa Internet has proved the demand and the willingness to pay at roughly KSh 1,500/month for an unlimited **household** connection. That product requires a household that (a) has a fixed dwelling worth wiring, (b) can commit KSh 1,500/month, (c) will be there next year.

**The larger market is the one that fails all three tests:** single-room renters, shared rooms, high-churn tenancy, income that arrives daily rather than monthly, and one device per person rather than one router per home. For that population the unit is not a household — **it is a device and a day.**

**KaziMesh's wedge: per-device, no-expiry, roams-everywhere, priced in daily denominations, sold through the landlord and the shopkeeper.** It does not cannibalize Poa; it sits below it, and the graduation path from KaziMesh monthly to a Poa household line is a partnership conversation, not a war.

### 1.4.3 Estate Captains — franchise design

The Captain is the answer to "Airtel had lower prices and still lost." **Distribution beat price.** Safaricom's moat is tens of thousands of agents. You cannot out-build that; you can out-align it.

| Element | Design | Rationale |
|---|---|---|
| Who | Shopkeeper (*duka*), M-Pesa agent, landlord's caretaker, barbershop, cyber café, salon | Already a trusted cash point with foot traffic and a fixed location |
| Capex | **KaziMesh funds the node.** Captain pays nothing upfront. | [Certain] Requiring host capex is how DePIN deployment models select for speculators over operators. Do not repeat it. |
| Earnings | **15–25% of revenue** from traffic on their node(s) + margin on device/voucher sales + onboarding bounty per activated credential | Aligns them to *traffic*, not coverage — the exact inversion of Helium's error |
| Obligations | Provide mains power and mount point; keep the node powered; first-line user support; sell passes | |
| Payment | Weekly **M-Pesa B2C** auto-settlement, with a verifiable Merkle inclusion proof of their own volume (§1.2.3) | Weekly, not monthly — matches their cash cycle and builds trust fast |
| Escalation | Captains with >3 nodes and good uptime become **Estate Leads** with a territory override | Creates a self-recruiting sales layer |

**Anti-fraud (learn this before it costs you):** Captains will be tempted to generate synthetic traffic to inflate their share. Mitigations: per-credential and per-device-MAC deduplication; flow-level sanity checks (traffic without plausible DNS/SNI diversity is synthetic); cap per-node share at a percentile of the estate distribution; randomized field audits. Build this in month 2, not month 12.

---

## 1.5 Regulatory path and the gates that must clear before hardware is ordered

[Likely — every item here must be verified against the Communications Authority of Kenya's current framework before commitment. Licence structures and fees change.]

| Gate | Requirement | Status/action |
|---|---|---|
| **Licence** | Kenya's **Unified Licensing Framework**. The relevant combination is **Network Facilities Provider Tier 3** (county/local-scope facilities) + **Application Service Provider** for the retail service. | Apply month 0. **Phase 0 workaround:** operate under an existing licensee's umbrella as a reseller/agent while your own application processes — this is the difference between launching in month 2 and month 8. |
| **Annual levies** | [Likely] ~0.4% of gross annual turnover to CA + ~0.5% to the Universal Service Fund. Verify current rates. | Model at 1.0% of revenue to be safe. |
| **5 GHz spectrum** | 5150–5350 and 5470–5725 MHz licence-exempt at restricted power (largely indoor); **5725–5875 outdoor PtP/PtMP typically requires a CA frequency authorization** — low-cost, not an auction. | Confirm exact outdoor EIRP limits and authorization process. This gates the Kibera anchor design. |
| **60 GHz / V-band (57–66 GHz)** | Licence-exempt in most jurisdictions. **Kenya's status must be independently confirmed with CA before ordering any 60 GHz hardware.** | **Hard gate. Do not order Terragraph/60 GHz gear until this is in writing.** If V-band is not exempt, the Pipeline spine falls back to licensed 70/80 GHz E-band PtP (cheap licences, higher hardware cost) or 5 GHz. |
| **Data protection** | [Certain] ODPC registration as data controller under the Data Protection Act 2019; DPIA; retention schedule; lawful basis for RADIUS accounting data. | Month 1. Non-negotiable, cheap. |
| **Public Wi-Fi user registration** | CA has pursued registration requirements for public Wi-Fi providers. Confirm current obligations. | Your Passpoint credential issuance already captures an identity link via M-Pesa — design the retention policy to satisfy this *minimally*. |
| **Consumer protection / tariff filing** | Confirm whether retail tariffs require CA notification. | |
| **VASP / securities** | **Only if the token path is taken.** [Likely] Kenya's 2025 virtual-asset framework brings VASPs under CBK/CMA supervision. | Recommendation: avoid entirely for v1 (§1.2.3). |

**Note on the regulatory posture:** none of the above requires Safaricom's cooperation, a spectrum auction, a wholesale agreement, an MVNO host, a handset change, or new silicon. **That is the design objective of the whole architecture** — every one of those dependencies is a named cause of death in §1.4.1.

---

## 1.6 Twelve-month rollout and honest targets

### 1.6.1 Phasing

**Phase 0 — Months 0–2: Legal, PoP, and 40-node proof. ~$180k**
- File NFP-T3 + ASP; simultaneously sign an umbrella reseller arrangement for immediate launch.
- ODPC registration, DPIA.
- **Confirm 60 GHz licence-exemption in writing. Do not order V-band gear before this returns.**
- Rack at iColo NBO1 or equivalent. 1× 10GE transit + **KIXP peering port on day one**. Obtain an ASN and IPv4/IPv6 allocation from AFRINIC (start early — this has lead time).
- Metro λ to a single Pipeline PoP.
- **40 leaf nodes across 3–4 Pipeline blocks. One landlord deal.**
- Stand up FreeRADIUS + OSU + Passpoint. **Measure the profile-install success rate by handset OEM** — this is the single most important technical metric of Phase 0. If it is below ~85% on your top 5 OEMs, fix it before scaling; Passpoint is the retention thesis.
- Daraja STK Push live. Parallel Airtel Money integration code-complete but dormant.

*Phase 0 exit criteria — do not proceed without all four:*
1. Passpoint install success ≥85% across top-5 handset OEMs
2. ≥45 paying users per leaf at ≥$2.20 blended ARPU
3. Node uptime ≥97%
4. 60 GHz legal status resolved

**Phase 1 — Months 3–5: Pipeline density. ~$420k → ~350 nodes**
- 60 GHz spine across Pipeline rooftops (or E-band/5 GHz fallback).
- **Landlord channel is the priority motion.** Target 25–40 buildings. One salesperson selling buildings outperforms twenty selling individuals.
- Recruit and train 60–90 Estate Captains. Weekly M-Pesa B2C settlement live.
- Launch sponsored-access tier: sign 2–3 CPA sponsors (a bank or SACCO, an edtech, a jobs platform).
- Deploy fraud detection (§1.4.2).
- **~350 nodes → ~28,000 users → ~5.6 TB/day**

**Phase 2 — Months 6–9: Second and third estates + wholesale. ~$700k → ~950 nodes**
- Mukuru, Kayole, Umoja — same vertical building stock, same playbook.
- Second PoP. Second transit carrier for diversity.
- **Apply for Google GGC and Netflix OCA** — you should be clearing thresholds around here. This cuts transit cost materially.
- Open the wholesale line: schools, clinics, SACCOs, small ISPs off your PoPs.
- **~950 nodes → ~76,000 users → ~15 TB/day**

**Phase 3 — Months 10–12: Kibera entry + consolidation. ~$550k → ~1,300 nodes**
- **Kibera on the correct architecture** — 5 GHz PtMP from tall anchors, not 60 GHz mesh. Higher node density per capita, lower ARPU, higher theft provisioning.
- Harden ops: NOC, field-team routing, spares depot, theft-response.
- Begin the EAP-TTLS → EAP-TLS migration on devices that tolerate it.
- **~1,300 nodes → ~100,000 users → ~19 TB/day**

### 1.6.2 The 50 TB/day target — why I'm revising it

Arithmetic:
- 50 TB/day = 1,500 TB/month = **1,500,000 GB/month**
- At 480 GB/leaf/month → **3,125 leaf nodes**
- At 80 users/leaf → **250,000 paying users**

**I disagree that 250,000 paying users in Nairobi informal settlements is a 12-month outcome.** The constraint is not capital — $594k of node capex is trivially fundable. The constraint is **ops density**: 3,125 nodes requires roughly 250–350 Estate Captains, a field team capable of ~15 installs/day sustained for a year, a support function handling ~250k users, and a collections function. Every organization that has tried to scale last-mile ops in Nairobi faster than this has broken on ops, not capital.

**Revised targets:**

| Horizon | Nodes | Users | TB/day | Cumulative capex |
|---|---|---|---|---|
| Month 6 | ~450 | 36k | **7** | ~$700k |
| **Month 12** | **~1,300** | **~100k** | **~19** | **~$1.9M** |
| Month 18 | ~2,300 | 180k | **35** | ~$3.1M |
| **Month 24** | **~3,300** | **265k** | **~53** | **~$4.2M** |

**50 TB/day is a month-22 to month-26 outcome on an aggressive-but-real execution curve.** Add NOC, salaries, licensing and working capital and the full 24-month requirement is **~$6.5–9M.** Presenting 50 TB/day as a 12-month number to an investor who can do this arithmetic costs you credibility you will need later.

**Network capacity check:** 50 TB/day = 50 × 8 × 10¹² bits ÷ 86,400 s = **4.63 Gbps average**, ~**14 Gbps peak** at 3× P:M. That is two or three 10GE uplinks across two PoPs. **The backbone is not the hard part, at any point on this curve.** Ops is.

### 1.6.3 Game theory: Safaricom's response, and staying under the retaliation threshold

[Guessing on the traffic denominator] Safaricom likely carries on the order of **1,500–2,500 TB/day** of mobile data in Kenya. At 19 TB/day you are **~1%** of their mobile data traffic; at 50 TB/day, **~2–3%**.

Their options and your read on each:

| Response | Their cost | Likelihood | Your counter |
|---|---|---|---|
| **Ignore you** | Zero | **High through month 12** | This is the gift. 1% of traffic is inside their measurement noise. **Build density quietly. Do not run a press campaign about "beating Safaricom" while you are small — it converts a rounding error into a board-level agenda item.** |
| **Cut bundle prices broadly** | Catastrophic — they would destroy a very large, very high-margin revenue line across their entire base to defend a 1–3% share loss. Classic incumbent's dilemma; their shareholders will not tolerate it. | **Low** | If it happens, it is a *win condition*: consumer prices fall nationally, which was the objective. You compete on no-expiry and unlimited, which they structurally cannot match without cannibalizing their own construct. |
| **Launch competing public Wi-Fi** | Cannibalizes the same bundle revenue | Medium-low | Same logic. They face the cannibalization; you don't. |
| **Restrict your M-Pesa Till** | Clean abuse-of-dominance exposure before the **Competition Authority of Kenya**, in a cost-of-living political climate | **Low but non-zero — plan for it** | §1.2.2 mitigations pre-built: Airtel Money live, bank rail, physical vouchers, CAK filing drafted and on the shelf. |
| **Lobby CA to restrict unlicensed Wi-Fi resale** | Politically toxic; you are visibly lowering consumer prices | Low-medium | Keep licensing immaculate. A properly licensed NFP-T3/ASP with ODPC registration is a hard target. **Compliance is a competitive weapon here, not overhead.** |
| **Acquire you** | Cheapest option for them | **Rises sharply above ~40 TB/day** | This is a real exit. So is Poa, so is Liquid/Cassava, so is Vodacom directly. |

**Strategic instruction: below ~25 TB/day, be boring.** Compete on product, not narrative. The retaliation threshold is the point at which you become a line item in someone's board pack, and you want to arrive there with 2,000 nodes and a licence, not with 300 nodes and a headline.

---

## 1.7 Winning Execution Strategy

**Build a licensed, Passpoint-federated, Wi-Fi 6 fixed-wireless transport layer across Nairobi's high-rise informal housing stock — starting in Pipeline, not Kibera — sold per-device with no expiry through landlords and shopkeepers, settled in KSh over M-Pesa, and peered at KIXP.**

Each clause bypasses a specific named cause of death:

| Design choice | Trap it avoids |
|---|---|
| Wi-Fi 6, not Wi-Fi 7 | TV White Space's non-existent silicon; paying 2.5× for features no client can use |
| ≤2 wireless hops | Municipal mesh capacity collapse |
| Pipeline (vertical) first | Deploying 60 GHz into a low-rise roofscape that breaks line-of-sight |
| Solar on ~12% of sites | ~$450k of misallocated capex |
| KIXP peering; no Squid/IPFS | The dead transparent-caching thesis under TLS 1.3/QUIC |
| CPA sponsorship, never CPM | BRCK Moja's 30× revenue shortfall |
| No expiry, flat per-GB | Attacks the actual margin mechanism instead of the radio |
| Estate Captains paid on **traffic**, node funded by KaziMesh | Helium's coverage-not-traffic incentive inversion |
| KSh ledger + Merkle root; no consumer token | Kenyan VASP/securities exposure; consumer friction |
| NFP-T3 + ASP, no spectrum auction, no Safaricom wholesale | The MVNO trap — every Kenyan MVNO that needed a host |
| Regulatory remedy treated as a call option, never the thesis | The shelved 2017 dominance study |
| Per-device, not per-household | Head-on collision with Poa, which is better capitalized |
| Quiet below 25 TB/day | Triggering incumbent retaliation before you have density |

**Capital:** ~$1.9M gets you to ~19 TB/day and ~100k users in 12 months, at node-level payback of roughly 1.5 months and 54% contribution margin. ~$6.5–9M gets you to 50 TB/day in 22–26 months.

**The three things that will actually kill this, in order:** (1) ops density — field install and support throughput; (2) Passpoint install failure on low-end Android skins, which would collapse the retention thesis back to captive portals; (3) 60 GHz licence-exemption turning out not to hold in Kenya, forcing a costlier spine. Test (2) and (3) in Phase 0, before the large hardware order. Hire for (1) from day one.
