# KaziMesh — Phase 0 Runbook: From Nothing to First Revenue

**Scope:** weeks 0–12. Ends with paying customers on 40 nodes, not with a launch event.
**Companion to:** [01-kazimesh.md](01-kazimesh.md)

---

## 0. Two corrections before the checklist

### 0.1 "Before monetisation" is the wrong frame, and the frame is itself the risk

[Certain] Every community-Wi-Fi corpse in East Africa — BRCK Moja included — died the same way: a long pre-revenue build, then an attempt to switch on charging against a user base that had been trained to expect free.

**KaziMesh should be taking money in week 8, from 40 nodes, from about 200 people.** Not because the revenue matters at that scale — it is roughly $600/month — but because **the only question Phase 0 exists to answer is whether people in Pipeline will actually pay you, repeatedly, without being chased.** Everything else in Phase 0 is instrumentation for that one measurement.

So the honest reframing:

> **There is no "before monetisation" phase. There is a legal minimum you must clear to accept a shilling lawfully, which is about 6–9 weeks of paperwork, and everything else is the experiment itself.**

The list in §2 is that legal minimum. It is shorter than most people expect. The long poles are the CA licence and the M-Pesa shortcode, and §3 shows how to route around the first one.

### 0.2 A material correction to the unit economics in 01-kazimesh.md

The node P&L in [§1.3.2](01-kazimesh.md#132-node-level-pl--the-number-that-decides-everything) used **gross** ARPU. It should be **net of the tax stack**, and the difference is large enough to matter in a diligence conversation.

[Likely — re-verify current rates with KRA] Excise duty on internet data services applies before VAT:

$$\text{net to you} = \frac{\text{price paid}}{1.15 \times 1.16} = \frac{\text{price paid}}{1.334}$$

A KSh 500 monthly plan yields **KSh 375** of revenue to KaziMesh. **Roughly 25% comes off the top before you have paid for anything.**

Restating the node P&L on net revenue:

| Line | Gross basis (original) | **Net basis (correct)** |
|---|---|---|
| Revenue / leaf / month | $240 | **$180** |
| Transport @ $0.023/GB | ($11) | ($11) |
| Power | ($3) | ($3) |
| Estate Captain share @ 20% of net | ($48) | ($36) |
| NOC / field ops / support | ($35) | ($35) |
| Bad debt, fraud, sharing | ($10) | ($10) |
| Payment processing | ($4) | ($4) |
| **Contribution margin** | $129 (54%) | **$81 (45%)** |
| **Payback on $197 capex** | 1.5 months | **2.4 months** |

**Still excellent.** A 2.4-month payback at 45% contribution margin is a very good infrastructure business. But present the 1.5-month number to an investor and the first person who asks "is that net of excise?" has just found the thing that makes them doubt every other number in the model.

**Fix this in the model before any fundraising deck is built.** And note the same haircut applies to Safaricom, so it does not change the competitive arbitrage — only your own margin.

---

## 1. The blocker that is not on anyone's checklist

**If you are not a Nairobi-based operator with ISP field experience, your binding constraint is not capital or licensing. It is a co-founder.**

My own analysis names ops density as the number-one killer of this business ([§1.7](01-kazimesh.md#17-winning-execution-strategy)). Ops density is not a thing you hire for after Series A. It is a thing that is either in the founding team or the company dies of it in month 14, with a full bank account.

**The specific person: someone who has run a Kenyan ISP or a large field-services operation, who already knows the landlord and *duka* networks in Embakasi, and who can be in Pipeline three days a week.** No amount of remote engineering substitutes.

If you do not have this person, **finding them is Phase 0 task #1 and everything else waits on it.** Not because the paperwork can't proceed — it can — but because committing capital to a last-mile business without a last-mile operator is how the $1.9M gets spent and nothing gets built.

---

## 2. The legal minimum to accept one shilling

Six items. Nothing else on this list blocks revenue.

| # | Item | Issuer | [Likely] time | [Likely] cost | Blocks revenue? |
|---|---|---|---|---|---|
| 1 | **Private limited company** | Business Registration Service, via eCitizen | 3–10 days | ~KSh 10–25k | **Yes — everything hangs off this** |
| 2 | **KRA PIN** + **VAT registration** + **excise registration for excisable services** | Kenya Revenue Authority | Days | Nominal | **Yes.** Excise on internet data services is the one people miss — confirm applicability with your tax adviser before you invoice anyone. |
| 3 | **Corporate bank account** | Any tier-1 bank | 1–3 weeks | — | **Yes** — M-Pesa settlement needs it |
| 4 | **M-Pesa Paybill or Till + Daraja production credentials** | Safaricom Business | **1–3 weeks + go-live review** | Setup fee + per-transaction | **Yes — this is your cash register** |
| 5 | **Right to operate**: your own CA licence, **or** a reseller arrangement under an existing licensee | Communications Authority of Kenya / a licensee | **See §3 — this is the fork** | See §3 | **Yes** |
| 6 | **ODPC data controller registration** + privacy notice + DPIA | Office of the Data Protection Commissioner | 2–6 weeks | Modest annual fee | **Yes** — you process personal data the moment you issue a credential or take an M-Pesa payment |

**Plus three documents you write, not apply for:**

- **Consumer terms and privacy notice** (ODPC-compliant, English + Kiswahili)
- **Estate Captain agreement** — revenue share, obligations, termination, equipment remains KaziMesh property
- **Landlord / building agreement** — access, power, riser rights, term, revenue mechanism

Have a Kenyan advocate draft all three once. Reuse forever. [Guessing] $3–6k.

### What you can defer past first revenue

Do not let any of these delay you:

| Deferrable | Until |
|---|---|
| 60 GHz V-band hardware and its licence question | Phase 1. **Run Phase 0 entirely on 5 GHz.** |
| KIXP peering | ~2 Gbps peak. Transit-only is fine at 40 nodes. |
| AFRINIC ASN + IP allocation | Start the application now (4–12 weeks), but NAT behind your upstream's addresses until it lands |
| Google GGC / Netflix OCA | You will not qualify until Phase 2 |
| Passpoint in production | **Test it in Phase 0 — ship it in Phase 1.** Captive portal is survivable across 40 nodes; it is not survivable across 1,300. |
| NVMe content library | Phase 1 |
| Any token or L2 | Indefinitely ([§1.2.3](01-kazimesh.md#123-the-bandwidth-ledger--and-the-honest-verdict-on-tokenizing-it)) |
| Wholesale / B2B line | Phase 2 |

---

## 3. The licensing fork — the single decision that sets your timeline

This determines whether you have revenue in week 8 or week 30.

| | **Route A: own CA licence first** | **Route B: umbrella reseller, licence in parallel** |
|---|---|---|
| Mechanism | Apply for NFP Tier 3 + ASP, wait, then launch | Contract with an existing CA licensee to operate as an authorised reseller under their licence, while your own application runs |
| [Guessing] Time to first revenue | **6–8 months** | **6–9 weeks** |
| Cost | Licence fees + ~1% of turnover in levies | The above, **plus the licensee's cut — [Guessing] 5–15% of revenue** |
| Risk | Burn 6 months of payroll learning nothing about whether anyone will pay | Dependency on a partner; must be papered properly; CA may have views on the structure |
| Verdict | **No** | **Yes — this is the route** |

**Take Route B.** The 5–15% you pay a licensee for six months is the cheapest market research you will ever buy, because the alternative is spending six months of salary to discover the same thing later. File your own NFP-T3 + ASP on day one and let it run in the background.

**Get Kenyan telecom counsel to paper the reseller arrangement properly and to confirm CA's current posture on it.** This is the one place in Phase 0 where cheap legal advice is expensive.

---

## 4. Team — four people, and the order to hire them

| Role | Why | When |
|---|---|---|
| **Field ops lead / Kenyan co-founder** (§1) | The named #1 killer of this business | **Before anything else** |
| **Network engineer who has run an ISP** | RADIUS, BGP, peering, PoP build. Not a generalist who has "done networking." | Week 1 |
| **Backend engineer** | Billing, quota enforcement, M-Pesa reconciliation, ledger | Week 2 |
| **You / commercial** | Landlord deals. One person selling buildings beats twenty selling individuals. | Day 1 |

**Fractional, not hired:** Kenyan telecom/regulatory advocate; data protection consultant for the DPIA; an accountant who understands excise on telecom services.

[Likely] Senior Nairobi engineering salaries run $2.5–6k/month. **Phase 0 burn is roughly $15–20k/month on people, against about $23k of total hardware.**

> **Read those two numbers again. Phase 0 is a payroll-and-paperwork exercise, not a hardware exercise.** The hardware is 12% of the spend. Anyone pitching you on the hardware has the wrong model of the business.

---

## 5. Phase 0 capital

| Line | Amount |
|---|---|
| 40 leaf nodes installed @ $65 | $2,600 |
| 40 DC UPS @ $32 | $1,280 |
| 5 distribution nodes (5 GHz) @ $450 | $2,250 |
| 1 PoP: BNG/router, switch, servers, rack, DC plant | $8,000 |
| Transit + metro + colo, 3 months @ ~$2,500 | $7,500 |
| Spares, tools, test gear | $2,000 |
| **Hardware and connectivity subtotal** | **$23,630** |
| Team, 4 FTE × 3 months | $48,000–60,000 |
| Legal, licensing, ODPC, counsel, company setup | $12,000–20,000 |
| Working capital and contingency (20%) | $18,000–22,000 |
| **Phase 0 total** | **$102,000–126,000** |

Add founder salary and a longer runway and you are at the **$180k** figure in [§1.6.1](01-kazimesh.md#161-phasing). Raise **$250k** if you can — six months of runway rather than three removes the pressure to declare Phase 0 a success prematurely, which is the most common way these gates get waved through.

---

## 6. Week-by-week

**Weeks 1–2 — Paper and people**
- Incorporate; KRA PIN; VAT and excise registration; open bank account
- File NFP-T3 + ASP with CA **and** open reseller negotiations with 2–3 licensees in parallel
- Start ODPC registration and AFRINIC ASN application (both have lead times — start them, forget them)
- Retain telecom counsel; commission the three contract templates
- **Close the field ops co-founder**
- Apply for M-Pesa Paybill/Till

**Weeks 3–5 — Build the cash register before the network**
- PoP racked; transit live; first 5 GHz link up
- **FreeRADIUS + quota engine + RFC 5176 CoA disconnect** working end to end
- **M-Pesa STK Push in sandbox, then production.** Build C2B confirmation *and* a nightly reconciliation job — [Certain] STK callbacks are unreliable, and small operators lose real money to unreconciled payments. Reconciliation is not a polish item.
- Double-entry ledger with idempotent accounting ingestion
- Sign the first landlord

**Weeks 6–8 — Deploy and switch on charging**
- 40 leaf nodes across 3–4 Pipeline blocks
- Recruit and train 8–12 Estate Captains; weekly M-Pesa B2C settlement live
- **Charge from day one.** No free period. A free period teaches your market the wrong price and you will never recover it.
- **Passpoint test in parallel:** install profiles on 50+ handsets across your top 5 OEMs and record the success rate by OEM. This is a measurement task, not a shipping task.

**Weeks 9–12 — Measure, and be willing to fail the gates**
- Daily cohort tracking: repeat purchase rate, days-to-second-purchase, GB/user, churn by Captain
- Fraud detection v1 (per-credential dedup, flow sanity checks, per-node share caps)
- Second landlord, third landlord
- **Gate review**

---

## 7. What "monetised" actually means — the Phase 0 gates

Do not proceed to Phase 1 without all four. These are the same criteria as [§1.6.1](01-kazimesh.md#161-phasing), restated as pass/fail:

| # | Gate | Threshold | If it fails |
|---|---|---|---|
| 1 | **Passpoint install success** | **≥85% across top-5 handset OEMs** | The retention thesis collapses to captive portals, which is the model that has never worked. Fix it or rethink the product. |
| 2 | **Paying users per leaf** | **≥45, at ≥$2.20 blended ARPU net of tax** | Below ~22 users/leaf the unit economics invert. Diagnose: coverage, price, or Captain quality. |
| 3 | **Node uptime** | **≥97%** | Usually power or theft. Both get worse at scale, never better. |
| 4 | **Repeat purchase rate** | **≥60% of week-1 buyers purchase again within 14 days** | **The most important gate and the one not in the original plan.** Anyone will buy once. The business is whether they buy again without being chased. |

**Gate 4 is the whole experiment.** Gates 1–3 are engineering. Gate 4 is the market telling you whether this is a business.

**And the discipline that matters:** these gates only work if you are willing to fail them. Write the thresholds down now, before you have data and before you are emotionally committed to 40 nodes you installed yourself. A gate you renegotiate after seeing the number is not a gate.

---

## 8. The three things most likely to go wrong in Phase 0

1. **M-Pesa reconciliation.** STK Push callbacks drop. Without a reconciliation job you will have users who paid and were not credited — and in a trust-based market served by shopkeepers, that is not a support ticket, it is a reputation event in an estate where everyone knows each other. **Build reconciliation in week 4, not week 20.**
2. **Estate Captain selection.** Your first 10 Captains set the culture of the whole network. Pick for existing trust and foot traffic, not for enthusiasm. Expect to remove 2 of the first 10 and plan the removal mechanism into the agreement before you need it.
3. **The free-period temptation.** Someone will argue for two weeks free "to build the base." [Certain] This is how Moja's market learned that Wi-Fi is free. **Charge from node one, day one.** If nobody pays at KSh 20/day, that is the finding Phase 0 exists to produce, and finding it in week 8 for $100k is enormously cheaper than finding it in month 14 for $2M.
