<h1 align="center">TR-Wellness-Regulation-Sim</h1>

<p align="center">
  <strong>Main question: "Can regulatory uncertainty be measured?"</strong><br>
  An artificial intelligence assisted regulatory impact analysis tool<br>
  for Turkey's Wellness Services Regulation and health tourism.
</p>

<p align="center">
  <a href="https://gamze-kose.github.io/TR-Wellness-Regulation-Sim/"><strong>▶ Open the tool</strong></a>
  &nbsp;·&nbsp;
  <a href="https://github.com/gamze-kose/TR-Wellness-Regulation-Sim"><strong>Main repository</strong></a>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/license-MIT-black.svg" alt="MIT">
  <img src="https://img.shields.io/badge/Bayesian%20network-learned%20from%20data-5B4DA6.svg" alt="Bayesian network">
  <img src="https://img.shields.io/badge/agent--based-simulation-177A5C.svg" alt="Agent-based simulation">
  <img src="https://img.shields.io/badge/rule--based-inference-A5382F.svg" alt="Rule-based inference">
  <img src="https://img.shields.io/badge/Monte%20Carlo-sensitivity%20analysis-555.svg" alt="Monte Carlo">
</p>

> **This repository is a mirror.**
> Development, releases and the full documentation live in the main repository
> maintained by Gamze Köse:
> **[github.com/gamze-kose/TR-Wellness-Regulation-Sim](https://github.com/gamze-kose/TR-Wellness-Regulation-Sim)**
> Please open issues and pull requests there.

---

## The problem

The provisions of a newly published regulation are not always applicable on
their own. Some are left to administrative determination, some concepts are
left undefined, some discretion is granted without criteria. Such uncertainties
are debated in legal texts but not measured.

Article 17 of the Wellness Services Regulation (4 July 2026, Official Gazette
33300) leaves the international health tourism authorisation of wellness centres
to the Ministry of Health. The Regulation on International Health Tourism
(26 April 2025, Official Gazette 32882) requires an authorisation certificate,
but whether an esenlik centre licence meets that regulation's definition of a
health facility has not been determined. **Annex 4 of the Wellness Services
Regulation, its inspection instrument, contains no question on it**. A centre
cannot know whether it is authorised to serve a visitor resident abroad.

The tool turns that gap into a number. It generates synthetic wellness centres
and health tourists, matches them against rules transcribed from the regulation
and returns a **three-valued** outcome:

| | |
|---|---|
| 🟢 **Compliant** | No rule is violated; service can be provided. |
| 🟣 **Undefined** | No rule is violated, but the status is undetermined because of a gap in the regulation. Four provisions can produce it. |
| 🔴 **Non-compliant** | At least one rule is violated. |

The third state is the core of the tool. It turns the *absence* of a provision
into a measurable category, and the method transfers to other legislation.

## Methods and techniques

The tool combines symbolic and probabilistic artificial intelligence. No
component is a black box: the learned tables, the rules applied and the sampling
trace can each be inspected from the interface.

| Technique | Where it is used |
|---|---|
| **Rule-based inference** (symbolic AI) | Twenty-one inspection questions from Annex 4 of the Wellness Services Regulation and twelve structural rules derived from the articles |
| **Three-valued logic** | Compliant / non-compliant / undefined, turning a regulatory gap into a measurable category |
| **Discrete Bayesian network** | Synthetic visitor generation by ancestral sampling in topological order |
| **Dirichlet-prior parameter learning** | Conditional probability tables estimated from the record set rather than hand-written |
| **Agent-based simulation** | Centres as individually tracked agents with their own state, service basket and detection counters |
| **Markov chain** | Year-to-year transition of the compliance state, with a separate matrix after a sanction |
| **Monte Carlo repetitions** | Median and 10–90 percentile band instead of a single estimate |
| **One-at-a-time sensitivity analysis** | Each parameter moved to its bounds to measure how much it drives each outcome |
| **Design of experiments** | Centre configurations drawn from legally distinguishable classes, not a generative model |
| **Feedback demand dynamics** | Licence applications rise as unmet demand grows and fall as capacity accumulates |

Generation, sampling and simulation are **reproducible**: the same
reproducibility key yields the same result, and no output depends on a language
model.

### Two design decisions worth noting

**The sanction ladder needs individual agents.** Annex 4 defines a distinct
sanction for the first, second and third detection of each rule. Centres are
therefore tracked one by one with their own detection counters; reduced to an
average, the ladder stops meaning anything.

**Structural rules and inspection rules act at different moments.** The twelve
structural rules — closed area, room count, institutional permits,
responsible-manager conditions — are checked when a licence is granted. The
twenty-one Annex 4 questions are checked during operation. Conflating the two
misstates what the regulation requires and when.

## How it works

Five steps, each consuming the output of the previous one.

### 01 · Data and model

The record set the visitor model learns from. There are no hand-written
probability tables; they are learned from these records. Records can be added
and deleted, and **every change retrains the model**, with the distributions on
the right updating immediately. The starter set of 240 records is not real
intake data and is expected to be replaced.

<p align="center">
  <img src="https://raw.githubusercontent.com/gamze-kose/TR-Wellness-Regulation-Sim/main/gorseller/en-01-data-and-model.png" alt="Record set and model training">
</p>

The learned tables are inspectable, and each row reports how many observations
it rests on; rows below five are flagged, because a probability learned from
three records is not a probability. Individual profiles can be drawn from the
model, with the draw traced node by node in topological order.

### 02 · Centres

Applications first pass the **structural rules**: closed area, room count,
institutional permits and responsible-manager conditions. The licensing decision
is three-valued: **some applications are left pending**, because Article 10(4)
requires premises appropriate to the nature of the service but gives no
criterion for appropriateness. The grounds for refusal are listed with their
article references — the most frequent is the three-year practice requirement
for the responsible manager (11/4-c), followed by the exclusivity requirement
(11/6).

Licensed centres are then assessed against the **Annex 4 inspection rules**.
Each card shows one of three states: no violation, violations but still
operating, or operations suspended. For most rules Annex 4 prescribes a warning
and a fine at the first detection and the centre keeps operating; only two rules
suspend operations at first detection.

<p align="center">
  <img src="https://raw.githubusercontent.com/gamze-kose/TR-Wellness-Regulation-Sim/main/gorseller/en-02-centres.png" alt="Grounds for refusal and generated centres">
</p>

A compliance cost layer prices the obligations the regulation imposes — closed
area, emergency room, full-time responsible manager, hospital cooperation,
information system, and the revenue lost to the prohibition on product sales —
and shows which business models remain viable. Fixed obligations affect small
operators disproportionately.

### 03 · Visitors

Synthetic health tourists are drawn from the trained model and matched against
the centres one by one. A visitor may go unserved for one of four reasons. A
person in convalescence is ineligible under Article 10(8)(d), which is a
regulatory prohibition. Finding no centre that offers the service sought, and
finding those centres at capacity, both arise from supply not meeting demand and
have nothing to do with the regulation. **Only for those resident abroad does
Article 17 come into play, and only then is the outcome undefined.**

Selecting a scenario opens a detail panel: the status of Article 17, the year it
comes into force, the acceptance rate after the determination, the inspection
rate and whether units fall within scope. Below is **S0 — Uncertainty
persists**: no determination is ever made, and a purple **Undefined** band
appears in the result strip.

<p align="center">
  <img src="https://raw.githubusercontent.com/gamze-kose/TR-Wellness-Regulation-Sim/main/gorseller/en-03-visitors.png" alt="Scenario S0, the undefined band is visible">
</p>

The same screen under **S1 — Permissive determination**. The determination is
made in 2028 and the purple band drops but does not vanish. The visitor count
and every other setting are identical across the two runs; the scenario is the
only thing that changes.

What remains is the study's most striking finding: **resolving Article 17 does
not remove uncertainty altogether**, because the undefined notion of
convalescence and the missing criterion for treatment purpose are resolved by no
scenario.

<p align="center">
  <img src="https://raw.githubusercontent.com/gamze-kose/TR-Wellness-Regulation-Sim/main/gorseller/en-03-visitors-s1.png" alt="Scenario S1, the undefined band is gone">
</p>

### 04 · Results

This step draws on three separate sources: the matching results and segment
breakdown come from the run just made, the sensitivity analysis is computed
separately on its own fixed baseline, and the multi-year projection was computed
in advance and is unaffected by any session setting.

The provision determining the outcome is recorded for every visitor. In the S0
run below, 83% were served and the remainder splits across four causes:
convalescence left undefined 6%, the bar of Article 10(8)(d) 6%, no
determination under Article 17 4%, and treatment purpose left without criterion
1%. Separating these matters for policy: a capacity shortage, a legal bar and a
gap in the regulation call for entirely different interventions.

The **undefined outcomes by source** table below isolates the undefined matches
and shows which gap each arises from. The breakdown by residence, age group and
service sought shows where the shortfall concentrates — thermal and balneology
demand is met noticeably less often than the rest, because that archetype
requires at least two of its core services in the same centre.

<p align="center">
  <img src="https://raw.githubusercontent.com/gamze-kose/TR-Wellness-Regulation-Sim/main/gorseller/en-04-results.png" alt="Results and segment breakdown">
</p>

A sensitivity analysis moves each free parameter to its lower and upper bound
and measures the change in the outcome, and a ten-year projection tracks eight
indicators across the five scenarios, including unlicensed operation and the
actual inspection rate.

### 05 · Uncertainty

A catalogue of **fourteen entries across six types**, three bearing directly on
health tourism, produced by reading the regulation article by article. Each
entry marks a gap that prevents a provision from being applied on its own, and
the list can be filtered by type.

**Four of these fourteen entries are wired into the simulation.** The
determination under Article 17, the undefined notion of convalescence and the
missing criterion for treatment purpose produce an undefined outcome at the
matching stage; the missing criterion for appropriate premises (Article 10(4))
does so at the licensing stage. Only the uncertainty under Article 17 is
resolved by the scenarios; **the other three persist in every scenario.** The
remaining ten are identified but not modelled.

Article 17 is only the first line. It is followed by the mismatch between the
heading and the text of the same article, the open-ended service list of Article
4(1)(c), the undefined notion of convalescence in Article 10(8)(d) — a
prohibition with severe sanctions resting on a concept without criteria — and
the absence of any criterion for space appropriate to the service in Article
10(4).

<p align="center">
  <img src="https://raw.githubusercontent.com/gamze-kose/TR-Wellness-Regulation-Sim/main/gorseller/en-05-uncertainty.png" alt="Uncertainties in the regulation">
</p>

---

## Findings

The figures come from the tool's default settings and are not validated against
real data. What matters is not the absolute values but the differences between
scenarios and the ranking of sensitivities.

- **Fourteen uncertainty entries** were identified, three bearing directly on
  health tourism; the most frequent type is discretion without criteria.
- **No inspection question** addresses the structural definitions of the
  regulation.
- Where no determination is made, the share of visitors resident abroad
  obtaining service falls to **59%** by the tenth year and **12%** of demand
  remains undefined; where it is made in 2028, the share rises to **80%**, yet
  **6%** still remains undefined because two further gaps are resolved by no
  scenario. These figures come from the multi-year projection, computed
  separately from the single-run results shown in the interface.
- Compliance with licensing conditions **almost wholly determines licence
  refusal** yet barely affects access to services, which is governed by capacity.
- In the compliance cost layer, **fixed obligations affect small operators
  disproportionately**; units in detached buildings fall below the viability
  threshold.

## Caveats

> **This is not a forecast.** There is no data to project a sector one month old
> across ten years. Entry rate, demand growth, compliance behaviour and cost
> figures are assumptions.

> **The data is synthetic.** All centres and visitors are generated. No real
> person or institution data is used.

> **The binding text is the Official Gazette.** The rule sets are transcriptions
> of the regulation and constitute neither legal interpretation nor advice.

## Usage

The tool runs entirely in the browser — no server, installation or data upload.

**Open it directly:** <https://gamze-kose.github.io/TR-Wellness-Regulation-Sim/>

**Or download** `index.html` from the main repository and double-click it; all
resources are embedded in the file and it works offline. The interface is
available in Turkish and English.

```bash
git clone https://github.com/gamze-kose/TR-Wellness-Regulation-Sim.git
```

---

## Full documentation

Screenshots of every step, the file map, the development commands and the
Turkish user guide are in the main repository:

**[github.com/gamze-kose/TR-Wellness-Regulation-Sim](https://github.com/gamze-kose/TR-Wellness-Regulation-Sim)**

## Related paper

> Köse, G. & Köse, U. (2026). Measuring Uncertainty: An Artificial Intelligence
> Assisted Regulatory Impact Analysis of the Wellness Services Regulation and
> Health Tourism. *INNOVAHEALTH 2026 — 3rd International Congress on Innovative
> Approaches in Health Sciences*, 2–5 September 2026, Kyrenia, TRNC.

## License

MIT