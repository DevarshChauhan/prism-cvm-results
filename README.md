# Results: Host-Side Ransomware Detection for AMD SEV-SNP and Intel TDX Confidential Virtual Machines Using Block I/O Request Metadata

Author: Devarsh Chauhan, Department of Information Technology, Shantilal Shah Engineering College, Bhavnagar, India.

This repository holds the results and raw logs behind every number, table and figure of the paper, so they can be checked independently. It does not contain the RanSMAP dataset or features derived from it: RanSMAP is licensed CC BY-ND 4.0 by its authors (https://github.com/manabu-hirano/RanSMAP).

## Contents

```
runs/
  ransmap_subset_manifest.json   the 530 RanSMAP runs used and their split (train_a, train_b, hw_shift, mix, variants)
  ransmap_files.json             the RanSMAP files those runs consist of
results/ransmap/
  main_t48_common.json           AUROC, PR-AUC, run-level alarms for all rules, bootstrap differences, importance (5 seeds)
  main_t48_storage.json          the same with a storage-only analysis horizon
  f1_t300.json, f1_t48.json      F1 under three protocols (20 seeds), always-alarm reference, paired comparisons
  lofo_t48.json                  held-out ransomware families and held-out benign programs (3 seeds)
  leak_t48.json                  per-window noise: detector AUROC and attacker accuracy (5 seeds)
  ow_by_program.json             rewrite-after-read fraction per program
  runlevel_unique.json           run-level alarms counted over unique runs, exact intervals, thresholds
  evasion.json                   simulated evasion and merged benign traces (transformed recorded metadata)
  enroll.json                    benign enrollment and lower thresholds for held-out families
  predictions_main.csv.gz        per-segment label, score and threshold for every seed, feature group and test
  environment_wsl.txt            software versions
results/cvm_snp/, results/cvm_tdx/
  summary_v2.json                decoded guest-to-host requests, block locality, workload identification
  cvm_out.tgz                    raw in-guest probe logs from the SEV-SNP and TDX cloud VMs (benign workloads only)
results/host_collector/
  summary_host.json              host versus guest block view, workload identification, transfer test, overhead
  prism_out.tgz                  raw host-side and guest-side block logs from the KVM host (benign workloads only)
results/host_collector_3h/
  summary_host.json, prism_out.tgz   a further 3-hour mixed benign session on the KVM host (summary and raw logs)
```

## Splits and seeds

The 530 runs are every run available in five RanSMAP configurations: ten per program on each of two memory configurations of the main machine, ten per program on a second machine, ten per mixed pair and ten per Conti variant, using the first 6 MB of each CSV file. In-distribution runs are shuffled with `numpy.random.default_rng(seed)` for seeds 0 to 4 (0 to 19 for F1, 0 to 2 for held-out tests) and split 70/15/15 by run. The mix and variant tests are scored against the benign test runs of the DDR4-2133 configuration.

## Mapping from the paper to files

| Paper item | File and field |
|---|---|
| Table 5 and Fig. 2 (segment AUROC), bootstrap differences | `results/ransmap/main_t48_common.json`: `summary.<group>.<test>.auroc`, `boot_loc_minus_mem` |
| Analysis-horizon check | `results/ransmap/main_t48_storage.json` |
| Table 6 (rewrite-after-read per program) | `results/ransmap/ow_by_program.json` |
| Table 7 (run-level alarms, unique runs) and Table 8 (alarm-rule sensitivity) | `results/ransmap/runlevel_unique.json`: `<group>.<split>.<rule>`; per-seed values in `main_t48_common.json` |
| Feature importance | `main_t48_common.json`: `importance_mix_storage+loc` |
| Table 9 (held-out families and programs) | `results/ransmap/lofo_t48.json` |
| Table 10 (simulated evasion) and concurrent benign load | `results/ransmap/evasion.json` |
| Table 11 (benign enrollment) and lower thresholds for held-out families | `results/ransmap/enroll.json` |
| Table 12 and Fig. 3 (F1) | `results/ransmap/f1_t300.json`, `f1_t48.json` |
| Fig. 4 and Table 13 (real CVMs) | `results/cvm_snp/summary_v2.json`, `results/cvm_tdx/summary_v2.json` |
| Table 14 (noise and attacker) | `results/ransmap/leak_t48.json` |
| Table 15 and host-collector results | `results/host_collector/summary_host.json`, `results/host_collector_3h/summary_host.json` |
| Any AUROC or alarm count, recomputed independently | `results/ransmap/predictions_main.csv.gz` |

Feature groups: `storage`, `storage+loc`, `storage+mem`, `storage+loc+mem`, `memory`. Tests: `test_iid` (same hardware), `test_hw` (new hardware), `test_mix` (ransomware with benign applications), `test_variant` (Conti variants). Alarm rules: `slideKofN` (alarm when K of the last N segments alert) and `cum3` (3 alerts counted from the start of the run).

No malware was run on any system controlled by the author; the cloud and host-collector logs come from benign workloads only. The analysis code is available from the author on request.

## Licence

Results and logs: CC BY 4.0 (see `LICENSE`).
