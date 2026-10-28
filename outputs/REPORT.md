# Football detection: early-stage demonstration

## Purpose and current scope
This experiment is a first step towards making player-detection and future assisted offside-review features accessible to amateur football. It detects players and balls in images. It does not establish offside, calibrate pitch positions, identify teams, determine the pass instant or adjudicate an offence. Deployment decision: supervised research demo and further evidence collection only.

## Data and run provenance
Roboflow augmented-startups/football-player-detection-kucab, version 8; classes ['football', 'player'].
Images: train 10308, validation 972, test 520.
Dataset fingerprint: `1ac4a89c81546b8edf96624ea79658d519b054fc116af03e2d22e69fd4abc5e0`.
Cross-split exact-image overlap counts: {'train/valid': 0, 'train/test': 0, 'valid/test': 0}. Match-level independence remains unverified.
Training mode: resume; model selection: final_checkpoint_fallback.
This training session: 59.4 minutes. Earlier runtime is not reconstructed.
Environment: {'python': '3.13.16', 'ultralytics': '8.4.191', 'torch': '2.11.0+cu130', 'cuda_available': True, 'device': 'Tesla T4'}.
Training history copied: True; curves copied: True. A resumed run may have incomplete pre-interruption history.

## Held-out detection comparison
Precision/recall below use confidence 0.25 and matching IoU 0.50. Custom AP uses confidence-retained predictions down to 0.001 and a shared matcher across baselines.

| model | class | TP | FP | FN | precision | recall | AP50 | mAP50-95 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| finetuned | football | 110 | 32 | 35 | 0.7746 | 0.7586 | 0.8002 | 0.4101 |
| finetuned | player | 2566 | 149 | 192 | 0.9451 | 0.9304 | 0.9542 | 0.6863 |
| zero_shot_coco | football | 1 | 0 | 144 | 1.0 | 0.0069 | 0.0173 | 0.0064 |
| zero_shot_coco | player | 132 | 54 | 2626 | 0.7097 | 0.0479 | 0.1841 | 0.0822 |
| predict_nothing | football | 0 | 0 | 145 | nan | 0.0 | 0.0 | 0.0 |
| predict_nothing | player | 0 | 0 | 2758 | nan | 0.0 | 0.0 | 0.0 |

Fine-tuned player AP50 minus zero-shot player AP50: +0.7701. This is the observed comparison for this run, not proof of generalisation to unseen amateur matches. Reference Ultralytics metrics are saved separately and use their own aggregation/matching conventions.

## Validation-selected policy and held-out outcome
Validation-only model threshold: 0.17.
Validation-only review band: [0.05, 0.90).
Fixed-policy test model-alone counts: TP 2584, FP 206, FN 174.
Fixed-policy test review simulation: TP 2627, FP 0, FN 131; frames reviewed 96.9%.

## Illustrative economic scenario
FN €40/missed player and FP €10/unmatched candidate are unmeasured policy penalties, with a 4:1 ratio. Review €0.100/frame = €18/hour × 15 seconds / 3600 + €0.025 overhead. Review is assumed perfect within the proposed band; unproposed misses remain missed. Volume is a hypothetical 60 matches × 6 candidate frames/day. Staffing assumes 6 productive hours/person/day and pooled work; peak concurrency is not modelled.

| strategy | scenario_eur_per_1000 | review_share | scenario_eur_per_day | reviews_per_day | review_hours_per_day | reviewer_FTE | people_if_work_is_pooled |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Predict-nothing proxy | 212153.846 | 0.0 | 76375.385 | 0.0 | 0.0 | 0.0 | 0 |
| Perfect person checks every frame (idealised) | 100.0 | 1.0 | 36.0 | 360.0 | 1.5 | 0.25 | 1 |
| Model alone at validation t*=0.17 | 17346.154 | 0.0 | 6244.615 | 0.0 | 0.0 | 0.0 | 0 |
| Model + validation review band [0.05, 0.90) | 10173.846 | 0.9692 | 3662.585 | 348.92 | 1.454 | 0.242 | 1 |

These are scenario comparisons, not realised savings or a forecast of refereeing outcomes. Compute, equipment, storage, support and real reviewer errors are excluded. See sensitivity.csv for validation-only policy changes under different assumptions.

## Decision and next steps
Continue as a supervised demo; do not use this model for official offside decisions. Collect permissioned amateur footage with match-separated evaluation, audit labels and detection failures, benchmark realistic hardware, then investigate tracking/team identity, camera calibration, pass timing, eligible body parts and expert-reviewed incident evaluation. Measure human speed and errors before revising the business assumptions.

## AI assistance and references
Original notebook code was generated with Claude. ChatGPT/Codex assisted this revision's resume workflow, prose and evaluation/reporting refinements. The author is responsible for reviewing the code, assumptions and actual results.
Dataset: https://universe.roboflow.com/augmented-startups/football-player-detection-kucab
Training/resume: https://docs.ultralytics.com/modes/train/
Validation: https://docs.ultralytics.com/modes/val/
Offside scope: https://theifab.com/laws/latest/offside/
