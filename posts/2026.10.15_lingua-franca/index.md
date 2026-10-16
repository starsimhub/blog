---
title: "Towards a lingua franca for epidemic modeling"
author: "Cliff Kerr"
date: "2026-10-15"
description: "One language for compartmental, metapopulation, and agent-based models, built on Starsim and tested against 23 models from 12 other frameworks."
categories: [Starsim, AI, News]
---

**TL;DR.** We are designing a *lingua franca* for epidemic modeling: one small vocabulary that can express compartmental, stochastic, metapopulation, and agent-based models, picks a solver for you, and lets models move between frameworks and paradigms. It will be released as Starsim v4. To keep ourselves honest, we have translated 23 models from 12 other frameworks into it. The translations agree with the originals in every case at fixed parameter values (a few differ in details, each of which is traced to a cause), and 20 of the 23 take fewer lines of code (often 3 to 6 times fewer). On speed it is a work in progress: 14 of 23 are as fast or faster than the original.

**Source.** Design documents, examples, and side-by-side comparisons: [starsim.org/lingua_franca_design](https://starsim.org/lingua_franca_design/) ([repository](https://github.com/starsimhub/lingua_franca_design)). Code: the [`v4` branch of Starsim](https://github.com/starsimhub/starsim/tree/v4) and the draft [pull request #1437](https://github.com/starsimhub/starsim/pull/1437).

## Motivation

There are dozens of epidemic modeling frameworks, and each is good at something: odin at writing down equations, EpiModel at dynamic sexual networks, SimInf at large numbers of herds linked by animal movements, LASER at speed. But a model written in one of them can't easily be run, checked, or extended in another. If an ordinary differential equation (ODE) model in R and an agent-based model in Python give different answers, it is currently very hard to say why.

Large language models (LLMs) make this both more pressing and more tractable. Why not just ask an LLM to write each disease model from scratch? Because AI is most accurate as a thin layer over code that already does most of the work (imagine an LLM doing a dataframe merge without access to `pandas`). A lingua franca gives AI tools a rigorous, deterministic target to write into, and gives us a fixed point to build skills, guardrails, and evaluations against. AI also makes the design work feasible: it can read every existing framework, see who handled each problem best, and at a minimum match that.

We built on Starsim because agent-based modeling is the most general paradigm. Any compartmental model can be written as an agent-based one, but not the other way around.

## What we did

The work has been built up in five stages, each of which is a section of the design site:

1. **[Approaches](https://starsim.org/lingua_franca_design/approaches/).** A review of 22 frameworks (including Atomica, EMOD, EpiModel, epydemix, flepiMoP, LASER, MEmilio, odin, SimInf, summer2, and Vivarium) drawn from the [disease modeling landscape](https://starsim.org/disease_modeling_landscape).
2. **[Best practices](https://starsim.org/lingua_franca_design/best-practices/).** A distillation of those reviews into positions on each part of building a model: time and units, stratification, mixing, interventions, calibration, stochasticity, and converting between paradigms.
3. **[Design](https://starsim.org/lingua_franca_design/design/).** A specification of the language as an extension of Starsim, from states and transitions through solvers, calibration, a JSON (JavaScript Object Notation) model spec, and the error messages.
4. **[Examples](https://starsim.org/lingua_franca_design/examples/).** Each framework's own example models, run in their native environments to produce reference results, plus their translations into Starsim v4.
5. **[Compare](https://starsim.org/lingua_franca_design/webapp/).** A side-by-side view of each original and its translation, with lines of code, run time, and how closely the results agree.

The design rests on a few principles:

- **The model is data.** States, transitions, and parameters are objects, so the diagram, the equations, the stratified model, and the saved file all come from the same source.
- **The solver is chosen for you, and can be changed.** By default, Starsim picks the simplest solver that represents the model faithfully (an ODE if every part has a compartmental form, agents otherwise) and says which. Asking for a stochastic, Gillespie, or agent-based run is one argument.
- **Approximations are named when they happen.** If a model has to be approximated to fit a solver, Starsim says so in one line.
- **Defaults are guessed and printed.** `sim.describe()` shows every inferred state, unit, dwell-time distribution, and mixing assumption, and marks which ones were guessed.
- **The vocabulary is small and counted.** Every public name is listed in the specification, and subclassing adds behavior, not vocabulary.

## What it looks like

Here is camdl's example SIR (susceptible-infectious-recovered) model, a closed epidemic observed through weekly reported cases with a negative binomial likelihood. camdl is itself a domain-specific language designed for exactly this kind of model:

```
compartments { S, I, R }

parameters {
  beta  : rate         in [0.05, 1.0]  ~ log_normal(mu = -1.0, sigma = 0.5)
  gamma : rate         in [0.01, 0.5]  ~ log_normal(mu = -2.0, sigma = 0.5)
  rho   : probability
  k     : positive
}

let N = S + I + R

transitions {
  infection : S --> I  @ beta * S * I / N
  recovery  : I --> R  @ gamma * I
}

observations {
  cases {
    columns       { time : time, cases : count }
    covers        = closing_at(time, 7 'days)
    projected     = incidence(infection)
    emit_schedule = every 7 'days
    cases         ~ neg_binomial(mean = rho * projected, r = k)
  }
}
```

And here it is in Starsim v4:

```python
import starsim as ss

beta  = ss.par(ss.perday(0.4),  bounds=[0.05, 1.0], prior=ss.lognorm_im(mean=-1.0, sigma=0.5))
gamma = ss.par(ss.perday(0.15), bounds=[0.01, 0.5], prior=ss.lognorm_im(mean=-2.0, sigma=0.5))
rho   = ss.par(0.6)
k     = ss.par(10)

S, I, R = ss.states(3)
infection = S >> I @ beta * I
recovery  = I >> R @ gamma
cases     = ss.outcome(rho * infection, every=ss.weeks(1), likelihood=ss.nbinom(n=k))

sir = ss.Disease(infection, recovery, cases, init=dict(I=10))
sim = ss.Sim(sir, total_pop=10_000, unit='day', stop=ss.weeks(12))
```

Transitions are written much like the equations (`S >> I @ beta * I` reads "S goes to I at rate beta times I", with the division by N handled by the default mixing). This runs as an ODE by default; `solver='binomial'` runs it as a stochastic chain-binomial model, and `sim.calibrate()` fits the parameters that have bounds to data with a matching name. The results agree with camdl's to within 0.02%.

The same vocabulary scales up. EpiModel's HIV example, with a care cascade and PrEP (pre-exposure prophylaxis) over two dynamic partnership networks, is 405 lines of R in the original and 62 lines in Starsim, runs in 2.5 seconds rather than 54, and agrees within the noise of the runs across all four scenarios.

## How it's doing

The test we set ourselves is simple: **a model should never be easier to write without the lingua franca than with it.** Concretely, each translation should take no more lines of model code than the original, run no slower, and give the same results. The models were chosen in three waves: the simplest example from each framework, eight models chosen to stress particular parts of the design (such as lognormal dwell times, vector-borne malaria, and drug-resistant tuberculosis), and then one complex example from each framework.

| | Passes | Notes |
|---|---|---|
| Results agree | 23 of 23 | Usually to within a percent or the noise of the runs, and several are identical to rounding error. Where details differ (such as an epidemic peaking a day earlier at a coarse timestep, or a calibration giving a point estimate rather than a posterior), the cause is identified |
| Fewer lines of code | 20 of 23 | The exceptions are epydemix's 7-line SIR (10 in Starsim) and two SimInf models whose original structure is built into SimInf's C code |
| As fast or faster | 14 of 23 | The largest gaps are agent-based runs of large populations (LASER's measles model in England and Wales: 131 seconds against 3) and SimInf's exact stochastic simulations |

Some highlights from the [full scorecard](https://starsim.org/lingua_franca_design/):

- **EMULSION's quickstart** (three state machines for age, gestation, and health in a pig farm, with births, a calendar, and quarantine capacity): 280 lines of YAML become 75 lines of Python, and the run time drops from 1.46 seconds to 0.25.
- **flepiMoP's vaccine scenarios** (two provinces with vaccination, waning, and stacked modifiers): 141 lines of configuration become 36, the run time drops from 96 seconds to 6.5, and every series agrees to within 1e-9.
- **Atomica's HIV care cascade** (by sex, with programs and three budget scenarios): 217 lines become 67, and the results are identical to rounding error.
- **Ghana measles** (stochastic SEIR in 260 districts with gravity mixing, seasonality, births, vaccination, and importation): 89 lines of NumPy become 20, with 3,046,095 infections over 15 years against 3,041,691.

The same work also ported [TBsim](https://github.com/starsimhub/tbsim) to the new design, including drug resistance as a strain dimension, a second-line regimen, and contact tracing, with ODE and agent-based runs that agree with each other.

## What's left

- **Speed.** The misses are mostly small models where the original takes a few milliseconds, and large agent-based or exact stochastic runs where frameworks like LASER and SimInf are heavily optimized. Closing these gaps is the main remaining work.
- **Interoperability.** Saving and loading a model as a JSON spec works for declared diseases and their stratifications, but not yet for networks, interventions, or custom Python code. Automated import from other frameworks is specified but not built.
- **Calibration.** There is no ABC-SMC (approximate Bayesian computation with sequential Monte Carlo) yet; agent-based models are fitted with Optuna (a best fit, not a posterior), and stochastic compartmental models with a likelihood for every data series get a posterior from particle MCMC (Markov chain Monte Carlo).
- **Open questions.** The design is still a draft, and the specification keeps a running list of [open questions](https://starsim.org/lingua_franca_design/design/19-open-questions.html), from default choices that are still guesses to names that may change.

AI tools (mostly Claude) were used to review frameworks, extract and run their examples, and write the translations. The assessments and design decisions are ours.

## Explore it

The best place to start is the [comparison tool](https://starsim.org/lingua_franca_design/webapp/), which shows each original model next to its translation. The [design overview](https://starsim.org/lingua_franca_design/design/00-overview.html) has the whole language on one page. We'd welcome feedback, especially from developers of the frameworks we've translated: if we've misrepresented your model or missed a better way to write it, please [open an issue](https://github.com/starsimhub/lingua_franca_design/issues) or [get in touch](mailto:info@starsim.org).
