# attributiongap

> Markov attribution scored against experimental truth, publishing a per-channel bias factor that corrects the daily numbers.

`FLAGSHIP` · **Marketing Analyst** · Advanced · ~4-5 weeks · Fintech - app-based lending acquisition

**Primary language:** SQL
**Tags:** `attribution`, `markov`, `dbt`, `sql`, `calibration`, `experimentation`

---

> **Implementation note.** The catalogue specifies **SQL** for this
> project and that remains the target. This repository ships a runnable
> **Python** reference implementation of the core differentiator so the
> behaviour is executable and tested today; port it to SQL as step one
> of your own build.

## The problem

The attribution tool says paid search drove 4,200 conversions. The holdout experiment says paid search's incremental contribution was 1,100. Both numbers appear in the same deck, quoted by different teams, and the organisation never resolves which one to budget against - so it budgets against the larger one, because it is the more comfortable number.

## ⭐ The differentiator

Builds a **Markov removal-effect attribution model and then explicitly scores it against experimental ground truth**, publishing a per-channel **attribution bias factor** used to correct the fast-moving daily numbers between infrequent experiments. A generic MTA project builds the Markov model, presents the channel split as the answer, and validates it against nothing - which is exactly how an organisation ends up with two numbers and no reconciliation.

This is the sentence to lead with when someone asks you to walk through the
project. Everything else in this repo exists to make it true and to prove it.

## Data

A synthetic journey generator producing user-level touchpoint paths with a **known causal structure** - some channels genuinely incremental, some purely correlated with intent - plus simulated holdout experiments providing ground truth. Structure informed by the openly licensed Criteo Attribution Modeling dataset.

> No paid API key is required to run or demo this project. Where a paid
> service would add value it is wired as an optional enhancement behind an
> interface with an offline mock as the default implementation.

## Stack

- SQL on DuckDB / PostgreSQL - journey reconstruction is the SQL artefact
- dbt with tests
- Python for the Markov computation
- TypeScript dashboard
- dbt tests + pytest

## Core capabilities

- Journey reconstruction with sessionisation, lookback windows and cross-device stitching rules stated explicitly rather than inherited from a vendor default
- Markov removal-effect attribution plus a Shapley comparison, with first/last-click as a deliberate contrast
- Experiment ingestion and per-channel bias-factor estimation with uncertainty
- Bias-corrected daily reporting that degrades gracefully as experiments age
- Channel trustworthiness scoring that directs where the next experiment should run

## Repository layout

```
models/journeys/
models/attribution/
src/markov/
src/calibration/
generator/
apps/web/
tests/
```

## Build plan

1. Generator with a known causal structure - some channels real, some pure correlation. This is the spine of the project.
2. Journey reconstruction in SQL. Make every windowing choice explicit and defensible.
3. Markov removal effect, then score it against the simulated experiments.
4. Bias factors and the decay model last - and be honest about how fast a bias factor goes stale.

## Testing strategy

Assert the Markov model's channel ranking matches true incrementality ranking on synthetic data where truth is known - **and quantify where it does not**, because the discrepancy is the finding. Assert bias factors recover the planted attribution-versus-incrementality gap within tolerance.

Tests assert **correctness**, not merely that the code runs. A green suite on
this repo is a claim about behaviour under adversarial conditions; treat any
test that would pass against a deliberately broken implementation as a bug in
the test.

## Quality & safety layer

Every corrected number carries the age of the experiment that calibrated it. A bias factor beyond its stated shelf life is reported as stale rather than silently applied.

## Measurable outcome

> Paid search is over-credited 3.8x and affiliate 2.1x, while organic is under-credited - corrected reporting shifts 1.9M of planned spend and names the next two experiments worth running.

State it in these terms — business units, not technical ones — in your CV
bullet and in the first thirty seconds of describing the project.

## Interview questions this project answers

- **Why does last-click attribution overstate paid search?**
- **What does a Markov removal effect actually measure?**
- **How do you reconcile attribution with an experiment?**

## What this deliberately is *not*

- Not a replacement for experiments. It is the bridge between infrequent experiments and daily decisions.
- Not a tracking pixel - it consumes journeys, it does not collect them.


## Run it now

```bash
python -m unittest discover -s tests -v   # the suite
python -m src.demo                        # the 60-second artefact
```

Requires Python 3.11+. The runnable core uses **only the standard
library** (including `sqlite3`), so there is nothing to install.

## Getting started

```bash
git clone <your-fork-url> attributiongap
cd attributiongap
pip install -e .
python -m generator --users 2000000
dbt build
python -m src.markov run
python -m src.calibration fit --experiments data/holdouts.csv
dbt test && pytest
```

Docker is supported but optional — every path above works on a plain
Windows/macOS/Linux laptop without a cloud account.

## Definition of done

- [ ] The differentiator above is implemented, and a test proves it
- [ ] The measurable outcome is produced by a command anyone can run
- [ ] `README` explains the one decision a generic version gets wrong
- [ ] CI runs the full suite on every push and is green on `main`
- [ ] A recruiter can see the headline artefact in under 60 seconds

## Licence

MIT — see [LICENSE](LICENSE).
