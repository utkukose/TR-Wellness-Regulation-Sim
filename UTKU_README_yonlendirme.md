<h1 align="center">TR-Wellness-Regulation-Sim</h1>

<p align="center">
  <strong>Can regulatory uncertainty be measured?</strong><br>
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

<p align="center">
  <img src="https://raw.githubusercontent.com/gamze-kose/TR-Wellness-Regulation-Sim/main/gorseller/en-01-data-and-model.png" alt="The tool">
</p>

---

## The problem

The provisions of a newly published regulation are not always applicable on
their own. Some are deferred to secondary regulation, some concepts are left
undefined, some discretion is granted without criteria. Such uncertainties are
debated in legal texts but not measured.

Article 17 of the Wellness Services Regulation (4 July 2026, Official Gazette
33300) defers the procedures for wellness services within health tourism to the
Ministry of Health, that secondary regulation has not been issued, and **Annex 4,
the inspection instrument, contains no question on it**. A centre cannot know
whether serving a visitor resident abroad complies with the rules.

The tool turns that gap into a number. It generates synthetic wellness centres
and health tourists, matches them against rules transcribed from the regulation
and returns a **three-valued** outcome:

| | |
|---|---|
| 🟢 **Compliant** | No rule is violated; service can be provided. |
| 🟣 **Undefined** | No rule is violated, but the status is undetermined because Article 17 has set no procedures. |
| 🔴 **Non-compliant** | At least one rule is violated. |

The third state is the core of the tool. It turns the *absence* of a provision
into a measurable category, and the method transfers to other legislation.

## Methods and techniques

The tool combines symbolic and probabilistic artificial intelligence. No
component is a black box: the learned tables, the rules applied and the sampling
trace can each be inspected from the interface.

| Technique | Where it is used |
|---|---|
| **Rule-based inference** (symbolic AI) | Twenty-one Annex 4 inspection questions and twelve structural rules derived from the articles |
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

| Step | What happens |
|---|---|
| **01 · Data and model** | The record set the visitor model learns from. Records can be added and deleted, and every change retrains the model. The learned tables report how many observations each row rests on. |
| **02 · Centres** | Applications pass the structural rules; refusals are listed with their article references. Licensed centres are assessed against Annex 4. A compliance cost layer shows which business models remain viable. |
| **03 · Visitors** | Synthetic health tourists are drawn from the trained model and matched against the centres. Convalescence is ineligible under Article 10(8)(d); residence abroad brings Article 17 into play. |
| **04 · Results** | The provision determining each outcome, segment breakdowns, a sensitivity analysis and a ten-year projection across five scenarios. |
| **05 · Uncertainty** | A catalogue of fourteen entries across six types, produced by reading the regulation article by article. |

<p align="center">
  <img src="https://raw.githubusercontent.com/gamze-kose/TR-Wellness-Regulation-Sim/main/gorseller/en-05-uncertainty.png" alt="Uncertainty catalogue">
</p>

Article 17 is only the first line of that catalogue. It is followed by the
mismatch between the heading and the text of the same article, the open-ended
service list of Article 4(1)(c), the undefined notion of convalescence in
Article 10(8)(d) — a prohibition with severe sanctions resting on a concept
without criteria — and the absence of any criterion for space appropriate to the
service in Article 10(4).

## Findings

The figures come from the tool's default settings and are not validated against
real data. What matters is not the absolute values but the differences between
scenarios and the ranking of sensitivities.

- **Fourteen uncertainty entries** were identified, three bearing directly on
  health tourism; the most frequent type is discretion without criteria.
- **No inspection question** addresses the structural definitions of the
  regulation.
- Where the deferred regulation is never issued, the share of visitors resident
  abroad obtaining service falls to **46%** by the tenth year and **11%** of
  demand remains undefined; where it is issued in 2028, the share rises to
  **86%**.
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
