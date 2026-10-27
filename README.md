# Cosmo Pulse

**Autonomous, offline health monitoring for astronauts: each crew member is their own baseline.**

> NASA Space Apps Challenge 2026 · Challenge: *Create Health Monitoring Software for Astronauts on Space Missions* · Local event: Casablanca

## The idea

On long-duration missions, astronauts must spot changes in their own health without a flight surgeon on the ground. Cosmo Pulse combines a **30-second daily voice check-in** with **wearable-style physiological signals**, compares them to the astronaut's **own adaptive baseline**, detects drifts (sleep, fatigue, stress, isolation) and uses an **offline LLM** to explain each alert and suggest a concrete countermeasure.

> "The best baseline for an individual is oneself."

## Architecture

```
Daily voice check-in ──┬─> Acoustic features (F0, HNR, speech rate)
                       └─> Speech content (Whisper + NLP: net positivity, lexical diversity)
Physiological signals ───> Time series (HR, respiration, temperature, activity, sleep)
                                │
                     Adaptive two-speed baseline  (slow = adaptation, fast = drift)
                                │
              Anomaly detection + fusion ──── Crew view (desynchronization)
                                │
                     Offline LLM + RAG (explain, recommend — never diagnose)
                                │
                        API + dashboard
```

Full design document: [`docs/architecture.pdf`](docs/architecture.pdf)

## Repository structure

```
src/cosmo_pulse/
  voice/       acoustic features + transcription + speech-content metrics
  physio/      scenario generator + time-series preprocessing
  baseline/    adaptive two-speed personal baseline
  detection/   anomaly detection + multi-domain risk fusion
  crew/        crew-level desynchronization
  llm/         offline LLM + retrieval over NASA countermeasure docs
  api/         FastAPI service
dashboard/     Streamlit demo
data/          local datasets (not committed)
knowledge_base/ documents for the RAG layer
docs/          architecture, references
notebooks/     exploration and evaluation
tests/
```

## Scope

**Core (must work end to end)**
- [ ] Voice check-in: acoustic features + net positivity
- [ ] Simulated physiological signals with adaptive baseline
- [ ] Alerts + LLM explanation
- [ ] Dashboard replaying a Mars-500-inspired mission scenario day by day

**Bonus (only once the core works)**
- [ ] Crew view (desynchronization)
- [ ] LSTM autoencoder vs Isolation Forest comparison
- [ ] Validation on StressID / XR4DRAMA

## Evaluation

- Detection delay (days between drift onset and first alert)
- Precision / recall per scenario
- False-alert rate on normal adaptation
- Comparison with fixed thresholds

## Quick start

```bash
cp .env.example .env
docker compose up --build
# API:       http://localhost:8000/docs
# Dashboard: http://localhost:8501
```

## Team

- **Oussama El-Boubekri** — physiological time series, baseline, anomaly detection, API
- **Meryeme Ramdi** — voice pipeline, speech content analysis, LLM / RAG

## Disclaimer

Hackathon prototype. Not a medical device. It does not diagnose conditions or prescribe treatment; thresholds and scenarios are illustrative.

## References

See [`docs/references.md`](docs/references.md).
