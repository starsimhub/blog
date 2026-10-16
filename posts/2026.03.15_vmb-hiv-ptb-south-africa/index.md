---
title: "Modeling vaginal microbiome therapies for HIV and preterm birth in South Africa"
author: "Robyn Stuart"
date: "2026-03-15"
description: "A Gates Foundation modeling study, built on Starsim, evaluates 2,400 vaginal microbiome therapy scenarios for HIV and preterm birth in South Africa. Findings have already reshaped how the next generation of clinical trials are defining success."
categories: [HIV, Bacterial vaginosis, Preterm birth, South Africa, Dashboards]
---

> **TL;DR.** Bacterial vaginosis (BV) is a disruption of the vaginal microbiome that affects up to half of women of reproductive age in sub-Saharan Africa, and it independently raises the risk of both HIV infection and spontaneous preterm birth. A new agent-based modeling study from within the Gates Foundation, built on the Starsim framework, asks what kind of vaginal microbiome therapy and what kind of clinical trial endpoint would actually deliver meaningful population impact. Across 2,400 scenarios in a South African cohort of 100,000 women, the team finds that durability drives HIV impact, efficacy drives preterm birth impact, and the probability of establishing durable community state type I (CST I), a *Lactobacillus crispatus* dominant microbiome, is the strongest single predictor of both.

**Key numbers**
- Reference scenario (80% efficacy, 12-month duration): 172,968 HIV infections averted (6.5% of projected total) and 251,201 preterm births (PTB) averted (11.1%) over 2035 to 2050
- Best-case scenario (80% efficacy, 18-month duration): 248,756 HIV and 249,571 PTB averted
- Duration effect at fixed 80% efficacy: 18-month products avert about 4 times as many HIV infections as 6-month products (248,756 vs 56,434)
- CST I establishment at 6 months: 59.6% for an 80% efficacy / 12-month product vs 18.7% for 50% efficacy / 6-month
- HIV per-act transmission probability multiplier for CST IV vs CST I: 1.8 times
- Cohort: 100,000 South African women aged 15 to 49; primary time horizon 2035 to 2050

**Source.** Cohen J, Snyder L, Dillard L et al. (2026). *VMB Modeling Study: Interactive Dashboard*. Gates Foundation. Explore the live [dashboard](https://vmb-dashboard.starsim.org/) and the [code](https://github.com/starsimhub/vmb_gr_2026q1). Manuscript in preparation.

---

## Motivation

Bacterial vaginosis is the most common vaginal condition in women of reproductive age in sub-Saharan Africa, with prevalence approaching half in some settings. Beyond its direct symptoms, BV has been independently linked to elevated risk of HIV acquisition and spontaneous preterm birth. The mechanism in each case runs through the vaginal microbiome: dysbiotic community states, notably CST IV (dominated by *Gardnerella* and *Prevotella* species), are associated with mucosal inflammation, breach of epithelial defenses, and altered host immune responses.

A new generation of vaginal microbiome therapies, known as live biotherapeutic products (LBPs), aims to restore a protective microbiome dominated by *Lactobacillus crispatus* (CST I). Early clinical work has shown promise, but two practical questions have remained open. What combination of product characteristics, including efficacy, durability, and probability of establishing CST I, would be needed to achieve meaningful population impact? And what should clinical trials measure to demonstrate that impact convincingly?

## What the team did

A team within the Gates Foundation built an agent-based model in [Starsim](https://starsim.org) v3 of a South African cohort of 100,000 women aged 15 to 49. Four modules run together in the standard Starsim simulation loop:

- A **BV natural history module** representing the vaginal microbiome as one of three community state types. CST I (*L. crispatus* dominant) is the protective state. CST III (*L. iners* dominant) is intermediate. CST IV (*Gardnerella* or *Prevotella* dominant) is the primary BV state. Monthly transition probabilities between states are empirically calibrated, with rates modified by sexual behavior, vaginal health and hygiene, and predisposition.
- An **HIV transmission module** with per-act transmission probabilities stratified by CST state (CST IV at 1.8 times CST I, based on meta-analytic estimates). The sexual contact network is a dynamic pair-formation model with calibrated partnership rates and durations.
- A **pregnancy and preterm birth module** in which PTB risk is driven by cumulative inflammation across the three pregnancy trimesters. Each month a woman's CST state contributes an inflammation cost (highest in trimester 1, CST IV up to 2.0 per month, CST I none). The cumulative score at delivery is translated to a relative PTB risk via a linear function. Persistent CST IV through pregnancy can reach a relative risk (RR) of around 1.7 times.
- A **vaginal microbiome (VMB) intervention module** representing product deployment. The model evaluates two product modalities (LBPs and antibiotics) across combinations of efficacy (0.3, 0.5, 0.7, 0.9), duration (3, 6, 12, 18, 24 months), CST I establishment rate (0.1 to 0.8), coverage (0.2 to 0.8), and target population (all women, BV symptomatic only, or pregnant women only). The reference delivery channel is symptom driven, with a 20% care-seeking rate per symptomatic episode; sensitivity analyses also explore asymptomatic prenatal screening.

The full combinatorial sweep generates 2,400 distinct scenarios. Each was run 100 times with different random seeds to characterize stochastic uncertainty, with reported intervals representing the 2.5th to 97.5th percentile of replicate outcomes. Calibration targets included HIV incidence and prevalence (UNAIDS estimates for South Africa), BV prevalence and CST distribution (WISH and CAPRISA cohorts plus a pooled meta-analysis), and CST transition rates (LACTIN-V trial longitudinal data and unpublished cohort follow-up). Calibration used Starsim's built-in framework with approximate Bayesian computation and a sequential Monte Carlo sampler.

## What they found

Across the 2,400 scenarios, four robust patterns hold.

**Duration drives HIV impact more than efficacy does.** At 80% efficacy, an 18-month duration averts roughly four times as many HIV infections as a 6-month duration (248,756 vs 56,434 averted over 2035 to 2050). Shorter-acting products limit the network-level reduction in transmission no matter how effective they are per exposure.

**Efficacy drives preterm birth impact more than duration does.** PTB outcomes scale closer to linearly with per-episode efficacy. A short-acting product administered during pregnancy can meaningfully reduce PTB rates if it effectively resolves BV during the gestational window.

**CST I establishment is the strongest single predictor of both outcomes.** The probability that a treated woman establishes durable *L. crispatus* dominance acts as a multiplier on every downstream outcome. At the 80% efficacy and 12-month reference scenario, CST I at 6 months reaches 59.6%; at 50% efficacy and 6-month duration, only 18.7%.

**The two outcomes are co-achievable.** The reference scenario simultaneously averts 172,968 HIV infections (6.5% of the projected total in this cohort) and 251,201 preterm births (11.1%) over 2035 to 2050. The best-case scenario (80% efficacy, 18 months) reaches 248,756 HIV and 249,571 PTB averted. Co-benefits across both outcomes are robust to scenario assumptions.

## Translation to clinical trial design

The modeling has already shaped how the next generation of LBP clinical trials is being designed. Investigators have shifted the primary endpoint of planned trials from simply reducing short-term BV recurrence to demonstrating a durable microbiome "cure" anchored in sustained CST I dominance. The new endpoint is more closely aligned with what the model identifies as the strongest predictor of long-term public health impact.

## Caveats

The model is calibrated to a South African setting, and the BV-HIV and BV-PTB associations used as inputs come from meta-analytic and cohort sources that carry their own uncertainty. The PTB mechanism (cumulative trimester inflammation translated to relative risk via a linear function) is mechanistically simplified compared to the full biology.

Care-seeking is modeled as symptom-driven at a uniform 20% per-episode rate, with prenatal screening as a sensitivity scenario. Cost assumptions are preliminary: the LBP per-course cost is evaluated across a wide $10 to $40 range reflecting current uncertainty, and the PTB cost input is under revision pending a systematic literature review.

## Explore the dashboard

All 2,400 scenarios, including disability-adjusted life years (DALYs) averted, cost per DALY, and uncertainty intervals, can be filtered and compared interactively at [vmb-dashboard.starsim.org](https://vmb-dashboard.starsim.org/). The model code is open on [GitHub](https://github.com/starsimhub/vmb_gr_2026q1). The manuscript is in preparation.
