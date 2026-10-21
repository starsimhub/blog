---
title: "FPsim+: modeling the feedback between contraception and empowerment"
author: "Robyn Stuart"
date: "2024-08-21"
description: "FPsim+ couples a custom Empowerment module to FPsim's contraceptive choice through the Starsim execution loop, with bidirectional feedback estimated from PMA Kenya longitudinal data. Family planning programs can now be evaluated for their impact on women's empowerment outcomes, not only on health outcomes."
categories: [Family planning, FPsim, Empowerment, Kenya]
---

> **TL;DR.** Family planning use and women's empowerment are well-known to be positively associated, and the association runs in both directions: empowered women are more likely to use contraception, and access to contraception in turn supports women's autonomy, paid work, and financial independence. Until now, no family planning model had captured this dynamic mechanistically. FPsim+ runs a custom Empowerment module alongside FPsim's contraceptive choice in the same Starsim simulation. The two modules talk to each other through the Starsim agent-state interface, so empowerment indicators shape contraceptive behavior and contraceptive behavior shapes empowerment indicators on every step. The same architectural pattern means empowerment can also be coupled to disease and intervention modules from elsewhere in the Starsim family.

**Key model components**
- Empowerment indicators tracked per agent: paid employment, decision over own wages, decision over major purchases, decision over clothing purchases, joint spending decisions with partner, savings, financial knowledge, financial goals
- Custom modules: Empowerment, EmpoweredChoice (contraceptive choice that conditions on empowerment state)
- Standard FPsim modules in use: Education, Pregnancy, demographics
- Estimation: survey-weighted generalized linear models (GLMs) fit to PMA Kenya longitudinal panels, with bidirectional dependencies between contraceptive use and empowerment indicators
- Calibration setting: Kenya, 2000 to 2020
- Calibration goodness of fit (full model): mean squared error (MSE) 0.087 ± 0.063 across targets including age-specific fertility rate (ASFR), total fertility rate (TFR), contraceptive prevalence (CPR), method mix, age at first birth, birth spacing, educational attainment, and paid employment

**Source.** Zimmermann M, Sanz-Leon P, Stuart R, Driano E, O'Brien M. (2024). *FPsim+: An Agent Based Model of Contraceptive Use and Empowerment*. VeriXiv 1:4. [DOI](https://doi.org/10.12688/verixiv.39.1). Code at [fpsim/kenya_empowerment](https://github.com/fpsim/kenya_empowerment).

---

## Motivation

The relationship between family planning (FP) and women's empowerment is bidirectional. Women with more decision-making autonomy, paid employment, and financial control are more likely to use contraception. The reverse holds too: access to and use of contraception lets women make more informed reproductive choices, which expands their ability to pursue work, education, and financial goals. One of the most-cited non-health benefits of FP programs is that they empower women, but the reverse direction (empowered women adopt FP) is also well-documented in the cross-sectional literature.

What has been missing is a mechanistic, dynamic understanding of how the two co-evolve at the individual level. Cross-sectional associations cannot tell us how a specific FP intervention propagates into empowerment outcomes, or how rising empowerment in a cohort feeds back into contraceptive uptake over time.

No existing FP model has captured this dynamic. Early versions of FPsim, like other FP models, did not include women's contraceptive intentions or the broader social impacts of FP. FPsim+ closes that gap.

## What we did

FPsim is built on [Starsim](https://starsim.org), the agent-based modeling platform that hosts a family of related disease and behavioral models (HPVsim, HIVsim, STIsim, FPsim, and others). From v3 onwards, FPsim is a fully integrated member of that family. This is the architectural change that makes the empowerment work possible without bespoke wiring. A new module, like the Empowerment module here, is a peer to any other Starsim module. It reads from and writes to the shared agent-state in the standard way, runs on the standard simulation step, and composes with other modules in a single simulation. Baseline FPsim now ships with an Education module that uses the same pattern.

For this work we added two custom modules to the standard Starsim execution loop:

- An **Empowerment module**. Each woman aged 15 to 49 carries a panel of binary empowerment indicators: paid employment, decision-making over her own wages, decisions over major purchases and clothing, joint spending decisions with her partner, savings, financial knowledge, and financial goals. These indicators are reassessed annually using survey-weighted generalized linear models fit to longitudinal PMA Kenya data. Predictors include the woman's demographics (age, parity, education, urban or rural residence, wealth quintile), her contraceptive use in the prior year, and her previous empowerment state.
- An **EmpoweredChoice contraceptive choice module**. Standard FPsim contraceptive choice depends on demographics, parity, and prior contraceptive use. EmpoweredChoice extends this so that a woman's empowerment state directly influences her probability of using contraception, her contraceptive intent, and her method choice.

The two modules run together with FPsim's Education and pregnancy modules in a single Starsim simulation. The result is a closed feedback loop. Empowerment indicators shape contraceptive behavior in the current year, and contraceptive behavior shapes empowerment indicators in the next.

We calibrated the model to Kenya from 2000 to 2020 against the standard FPsim demographic targets (age-specific fertility rate, total fertility rate, contraceptive prevalence, method mix, age at first birth, birth spacing) and against educational attainment and paid employment. The full empowered model reaches MSE 0.087 ± 0.063 across these targets. A simplified variant that excludes the empowerment integration reaches MSE 0.29 ± 0.51 on the same targets, so the empowerment-enabled model also calibrates noticeably better to the Kenya data.

## What this enables

With this architecture, a researcher modeling FP in Kenya can ask not only how a programmatic change in contraceptive access affects health outcomes (unplanned pregnancies, short birth intervals, maternal health), but also how the same change propagates into empowerment outcomes: paid employment, financial autonomy, decision-making power, savings, financial goals. The propagation happens through the calibrated GLM relationships and through the dynamic feedback loop, so it is responsive to the trajectory each cohort takes, not just to an aggregate elasticity.

The same composability extends across the Starsim family. Because the Empowerment module is a peer to any other Starsim module, it can be combined with the HIV transmission and treatment modules in HIVsim, the STI modules in STIsim, or the cervical disease modules in HPVsim. Empowerment trajectories can interact with infection risk, health-seeking behavior, and treatment uptake in the same simulated population.

## Caveats

The GLM coefficients that drive the empowerment dynamics are estimated from PMA Kenya. They are Kenya-specific. Transferring the empowerment integration to another setting requires recalibration of those relationships on local longitudinal data; the modeling architecture is portable but the empirical content is not.

The empowerment indicators are constrained by what PMA measures longitudinally: paid work and financial autonomy dominate. Broader notions of empowerment, including political, social, and health-decision empowerment, are not captured here.

## Where this fits

FPsim+ is a concrete demonstration that the Starsim family abstraction is doing what it was designed to do. A new behavioral module (empowerment) can be written once, composed with FPsim today, and composed with disease-side Starsim modules tomorrow, all without bespoke integration code. The Kenya empowerment work is one application of that pattern. Coupling empowerment to HIV-related risk and treatment trajectories, to HPV vaccination access, or to STI screening uptake is now mechanically straightforward.
