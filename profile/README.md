# Suni Muhendis

**Can a language model design real engineering hardware?**

We build deterministic physics environments that judge machine-generated
designs, a public benchmark that measures how today's models do, and our own
models trained against the same referee.

[**Leaderboard**](https://suni-muhendis.github.io/sm-bench/leaderboard/) ·
[**Website**](https://suni-muhendis.github.io/sm-bench/) ·
[**Models on Hugging Face**](https://huggingface.co/SM-AI)

## How it works

A model receives a design task (operating conditions and requirements) and
answers with a complete design as JSON. The environment judges it in four
stages, cheapest first, and the first failure stops evaluation:

```
design (JSON) → schema → design rules → physics simulation → score ∈ [0, 1]
```

The same referee, at the same pinned version, judges every model, so results
are reproducible and comparable.

## Latest results

`heat_exchanger_suite_v2`: 30 heat-exchanger design tasks at three difficulty
levels, one answer per task, judged with score V5.

| Model | Easy | Medium | Hard | Score when met |
|---|---|---|---|---|
| claude-opus-5 | **0.57** | **0.30** | **0.14** | 0.57 |
| deepseek-v4.1-flash | 0.43 | 0.24 | 0.13 | 0.48 |
| deepseek-v4-pro | 0.41 | 0.21 | **0.14** | 0.42 |
| [**SM_heat_exchanger_v1**](https://huggingface.co/SM-AI/SM_heat_exchanger_v1) (ours, fine-tuned from Qwen2.5-7B-Instruct) | 0.53 | 0.11 | 0.10 | **0.79** |
| gpt-oss-120b | 0.37 | 0.22 | 0.12 | 0.39 |
| Qwen2.5-7B-Instruct (base model) | 0.35 | 0.06 | 0.05 | 0.35 |

*Easy / Medium / Hard:* mean score per level. Score V5 gives 0.3 for a design
that meets the duty target within both pressure-drop limits, and up to 1 the
closer its cost comes to a well-engineered reference design (on a logarithmic
scale); a design that misses a requirement earns at most 0.15. *Score when
met:* mean score of the answers that met every requirement.

claude-opus-5 leads every level. Our model scores above its own base model at
every level, and its working designs are the closest to cost (0.79), but it
sizes close to the target and misses it more often than the frontier models.

**Read before comparing:**

- *Different tasks behind the two scores.* Our model met the requirements only
  on easy tasks; the other models also met them on harder ones.
- *It knows this referee.* It was trained against the same simulator and cost
  model on other tasks. The other models answer zero-shot: the prompt gives
  the cost thresholds but not how cost is computed.
- *Meeting the requirements is not good design on its own.* The untuned base
  model meets them on every easy task by returning nearly the same oversized
  design for every task; the loose easy limits let it pass, and it earns
  nothing for cost.
- *The other models are not at their best.* Lowest reasoning effort, one
  answer per task. More reasoning, or the cost model in the prompt, could
  change these results; both are next.

[Full results →](https://suni-muhendis.github.io/sm-bench/leaderboard/)

## Current limitations and next steps

These results are a first, deliberately narrow measurement:

- **Minimal reasoning.** Every model runs at the lowest reasoning effort it
  offers, so the results measure direct design intuition rather than extended
  thinking. Higher reasoning budgets are next.
- **One run per model.** Each model answered each task once, at temperature 0
  where the model accepts it. API models are not fully deterministic even
  then, so a repeated run can move a score; with ten tasks per level, close
  scores (within a few hundredths) should be read as ties. Repeated runs, to
  measure this noise and report it as an interval, are next, together with
  the effect of sampling temperature and of several attempts per task
  (best-of-N).
- **Zero-shot only.** Models never see the simulator's verdict. A
  feedback-driven track is coming, where an agent can call the physics
  simulator, read the result and revise its design before answering.
- **One domain.** Heat exchangers only for now; see the roadmap below.

## Repositories

| Repository | What it is |
|---|---|
| [sm-bench](https://github.com/suni-muhendis/sm-bench) | SM-Bench: benchmark runners, recorded results, leaderboard and site |
| [sm-heat-exchanger](https://github.com/suni-muhendis/sm-heat-exchanger) | Heat-exchanger environment: shell-and-tube and concentric-tube designs |
| [sm-core](https://github.com/suni-muhendis/sm-core) | The shared evaluation contract every environment implements |
| [sm-throughflow](https://github.com/suni-muhendis/sm-throughflow) | *Experimental:* axial-compressor throughflow environment, validation in progress |

## Roadmap

- ✅ **Heat exchangers**: available
- 🔜 **Turbomachinery**: axial compressors first, then turbines
  ([sm-throughflow](https://github.com/suni-muhendis/sm-throughflow), experimental)
- 🔜 **Rotordynamics**: shafts, bearings, critical speeds
- 🗺️ **3D turbomachinery flow** and further domains

## Team

**Mehmet Fatih Reyhan** ([@mfreyhan](https://github.com/mfreyhan)) and
**Ertuğrul Şahin** ([@ertugrl](https://github.com/ertugrl)), co-founders.

Code is Apache-2.0; benchmark data is CC BY 4.0.
