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

`heat_exchanger_suite_v1`: 30 heat-exchanger design tasks at three difficulty
levels, one answer per task.

| # | Model | Mean score |
|---|---|---|
| 1 | [**SM_heat_exchanger_v1**](https://huggingface.co/SM-AI/SM_heat_exchanger_v1) (ours, fine-tuned from Qwen2.5-7B-Instruct) | **0.329** |
| 2 | claude-opus-5 | 0.284 |
| 3 | deepseek-v4.1-flash | 0.271 |
| 4 | deepseek-v4-pro | 0.269 |
| 5 | gpt-oss-120b | 0.264 |
| … | | |
| 9 | Qwen2.5-7B-Instruct (base model of SM_heat_exchanger_v1) | 0.176 |

Fine-tuning lifts the base model's score by 87%, from 0.176 to 0.329, on tasks
it never saw. [Full leaderboard →](https://suni-muhendis.github.io/sm-bench/leaderboard/)

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
