# MemoryGraph AI
contact me for source code : lakmaljayawardana05@gmail.com


**Evidence-grounded visual episodic memory, demonstrated on synthetic workshop footage.**

MemoryGraph AI converts video observations into object-centric events, records frame citations, and answers a small set of natural-language questions without fabricating unsupported facts. It includes a sample video, a browser dashboard, event data, tests, and a research-style manuscript.


## Watch the demo

- `artifacts/MemoryGraph_AI_demo.mp4`: 39-second annotated demonstration.
- `artifacts/MemoryGraph_synthetic_input.mp4`: original fully synthetic camera clip.
- `artifacts/memory.json`: automatically extracted visual evidence + graph.
- `artifacts/evaluation.json`: transparent single-clip evaluation, not real-world accuracy.

## Run on Windows CMD / macOS / Linux

```bash
python -m venv .venv
# Windows CMD: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
python scripts/build_demo.py
python -m memorygraph.server
```

On Windows, you can instead double-click **RUN_WINDOWS.cmd** to set up and launch the included demo.

Open **http://127.0.0.1:8000**. Start the video or click the evidence timestamps in the timeline. Try:

- `Who moved the red component?`
- `Where was the red component last seen?`
- `When was the handoff?`
- `Show the object timeline`
- `What did Alice say?` (correctly abstains)

### API

| Endpoint | Purpose |
| --- | --- |
| `GET /` | Interactive dashboard |
| `GET /video` | Annotated demo clip |
| `GET /api/memory` | Event graph + last-seen evidence |
| `GET /api/evaluation` | Single-clip metrics and caveats |
| `POST /api/query` | `{"question":"Who moved the red component?"}` |
| `GET /api/health` | Service status and operating mode |

## Architecture

```
SIMULATED CAMERA FOOTAGE
          |
  HSV segmentation (CV baseline)
          |
  per-frame x/y bounding boxes
          |
  object-zone and proximity state machine
          |
  evidence-linked temporal event graph
          |
  deterministic question router / abstention
          |
  FastAPI JSON API + browser dashboard
```

The vision pipeline ingests **pixels only**. Ground-truth event timestamps are stored in a separate file and used by the evaluation script only. `memory.json` is produced by the video analysis, not by copying ground truth.

## Evaluation

Run: `python scripts/build_demo.py`, then view `artifacts/evaluation.json`. Run `python scripts/robustness.py` to repeat the controlled image-perturbation diagnostics. For browser-compatible H.264 video encoding, install FFmpeg and add it to PATH; if FFmpeg is absent the OpenCV-generated MP4 may not play in all browsers, though VLC can play it.

Results describe **one scenario**, with no independent test set. The demonstration's F1 must **not** be interpreted as real-world reliability. Event timestamp tolerance is ±2 s. The "confidence" values are heuristic rules, not statistically calibrated.

## Research extensions

1. Replace color-coded segmentation with a detector (e.g. YOLO) on consented, annotated clips.
2. Introduce ByteTrack or another tracking-by-detection system with occlusion/ID-switch tests.
3. Use contact and motion classifiers with explicit uncertainty rather than proximity-only handoff.
4. Store relational events in an append-only temporal database with provenance fields.
5. Benchmark natural-language temporal localization on Ego4D NLQ (subject to licence and access requirements).
6. Add local VLM verification over retrieved evidence frames and measure hallucinations/abstention.
7. Conduct held-out, cross-camera and privacy-preserving studies with formal data governance.

## Research integrity and data ethics

All generated frames are synthetic. No real people, private footage, personal data, or external API credentials are bundled. The example labels 'Alice' and 'Ben' identify synthetic visual markers, not individual humans. Avoid any use for disciplinary, biometric, covert surveillance or safety-critical decisions. Before working with real footage, establish a lawful basis, notice, access controls, retention schedule, and a privacy impact assessment where required.

## Repository structure

- `memorygraph/` Computer vision, event extraction, graph memory, QA, API, evaluation.
- `scripts/build_demo.py` Reproducible end-to-end demo generator.
- `web/index.html` Browser dashboard with video and evidence navigation.
- `tests/` Unit tests.
- `docs/MemoryGraph_AI_Research_Manuscript.md` Research draft and future experiments.
- `docs/LinkedIn_Post.md` Copy-ready public caption.
- `RUN_WINDOWS.cmd` One-click local startup (Python still required).
- `docs/GitHub_Release_Post.md` GitHub release copy.

## Academic starting points

- Yan et al. (2025/NSDI 2026), **AVA: Towards Agentic Video Analytics with Vision Language Models**. https://www.usenix.org/conference/nsdi26/presentation/yan
- Ren et al. (2025), **VideoRAG: Retrieval-Augmented Generation with Extreme Long-Context Videos**. https://arxiv.org/abs/2502.01549
- Malik et al. (2025), **RAVU: Retrieval Augmented Video Understanding with Compositional Reasoning over Graph**. https://arxiv.org/abs/2505.03173
- Zhang et al. (2022), **ByteTrack**. https://www.ecva.net/papers/eccv_2022/papers_ECCV/html/315_ECCV_2022_paper.php
- Ego4D **Episodic Memory benchmark**. https://ego4d-data.org/docs/benchmarks/episodic-memory/

## Status

`v0.1 — reproducible research demonstrator`; not a peer-reviewed publication, generalized CV product, or validated PhD contribution.
