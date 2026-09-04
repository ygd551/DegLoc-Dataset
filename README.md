# GeoDegen-Dataset

A Livox Avia LiDAR-inertial dataset targeting **geometrically degenerate environments** — tunnels, long corridors, open flat areas, and other scenes where point-to-plane / point-to-edge constraints become ill-conditioned.

<!-- Badges: uncomment and fill in once available
[![Paper](https://img.shields.io/badge/paper-arXiv-b31b1b.svg)](TODO)
[![License](https://img.shields.io/badge/license-CC%20BY--NC--SA%204.0-blue.svg)](LICENSE)
-->

---

## 1. Motivation

Most public LiDAR SLAM benchmarks are collected in structurally rich environments, where geometric constraints are well-conditioned in all six degrees of freedom. In practice, robots frequently operate in scenes that provide **insufficient geometric constraints** along one or more directions:

- long tunnels and underground corridors (translation along the axis is unconstrained)
- open flat ground / parking lots (few vertical structures)
- narrow staircases and featureless walls
- large-scale symmetric or repetitive structures

In such scenes the Hessian of the scan-registration problem becomes **rank-deficient**, and state estimation drifts or diverges even for otherwise robust odometry pipelines.

This dataset provides sequences specifically recorded in these conditions, together with ground-truth trajectories, to support:

- benchmarking the robustness of LiDAR / LiDAR-inertial odometry
- development and evaluation of degeneracy detection and mitigation methods
- studying sensor-fusion strategies under partial observability

---

## 2. Sensor Setup

| Item | Specification |
|---|---|
| LiDAR | Livox Avia (non-repetitive scanning, FOV 70.4° × 77.2°) |
| IMU | Built-in BMI088, 200 Hz |
| Point rate | 240,000 pts/s (first return) |
| Ground truth | TODO — e.g. Leica MS60 / MoCap / RTK-GNSS |
| Platform | TODO — handheld / UGV / UAV |
| Extrinsics | See `calib/` |

> Replace the TODO rows with your actual configuration. If ground truth is only available for part of the sequences, state that explicitly here — it is the first thing reviewers look for.

<!-- Add a photo of the sensor rig here; it substantially improves reproducibility.
<p align="center"><img src="docs/platform.jpg" width="500"></p>
-->

### Coordinate frames and extrinsics

The LiDAR-to-IMU extrinsic parameters are provided in `calib/extrinsic.yaml`:

```yaml
extrinsic_T: [ 0.04165, 0.02326, -0.0284 ]
extrinsic_R: [ 1.0, 0.0, 0.0,
               0.0, 1.0, 0.0,
               0.0, 0.0, 1.0 ]
```

All ground-truth trajectories are expressed in the **IMU (body) frame** unless otherwise noted.

---

## 3. Sequences

| Sequence | Scene type | Degeneracy direction | Duration | Length | Ground truth |
|---|---|---|---|---|---|
| `tunnel_01` | Straight tunnel | Longitudinal (x) | 3 min 20 s | 180 m | ✅ full |
| `corridor_01` | Indoor long corridor | Longitudinal (x) | 2 min 45 s | 120 m | ✅ full |
| `open_ground_01` | Open flat area | Horizontal (x, y) | 4 min 10 s | 260 m | ⚠️ partial |
| `stairs_01` | Narrow staircase | Rotational (yaw) | 1 min 50 s | 40 m | ❌ none |

> The table above is a template — fill in your real sequences. Listing the **dominant degeneracy direction** per sequence is what makes this dataset more useful than a generic collection, so keep that column.

---

## 4. Data Format

Data is released as ROS bags:

```
GeoDegen-Dataset/
├── calib/
│   ├── extrinsic.yaml
│   └── imu_intrinsic.yaml
├── sequences/
│   ├── tunnel_01/
│   │   ├── tunnel_01.bag
│   │   └── tunnel_01_gt.txt
│   └── ...
└── tools/
    ├── evaluate.py
    └── bag_to_pcd.py
```

### Topics

| Topic | Type | Rate | Note |
|---|---|---|---|
| `/livox/lidar` | `livox_ros_driver/CustomMsg` | 10 Hz | per-point timestamp preserved |
| `/livox/imu` | `sensor_msgs/Imu` | 200 Hz | |

Point clouds are published in the **custom Livox message format** to retain per-point timestamps, which are required by most LiDAR-inertial pipelines for motion compensation. A conversion script to `sensor_msgs/PointCloud2` is provided in `tools/`.

### Ground-truth format

TUM format, one pose per line, space-separated:

```
timestamp tx ty tz qx qy qz qw
```

---

## 5. Download

| Source | Link |
|---|---|
| Google Drive | TODO |
| Baidu Netdisk | TODO (code: `xxxx`) |
| OneDrive | TODO |

Total size: **TODO GB**. Sequences can be downloaded individually.

> For datasets above a few GB, avoid hosting the raw data in the Git repository itself. Keep the repo for documentation and tools, and host the bags on a drive service or Zenodo. Zenodo additionally gives you a permanent DOI, which is worth having if a paper is attached.

---

## 6. Quick Start

```bash
# 1. Clone
git clone https://github.com/<user>/GeoDegen-Dataset.git
cd GeoDegen-Dataset

# 2. Play a sequence
rosbag play sequences/tunnel_01/tunnel_01.bag --clock

# 3. Evaluate a trajectory against ground truth (requires evo)
pip install evo --upgrade
evo_ape tum sequences/tunnel_01/tunnel_01_gt.txt result.txt -va --plot
```

### Tested with

The sequences have been verified to run with the following open-source pipelines:

- FAST-LIO2
- Point-LIO
- LIO-SAM (requires conversion to `PointCloud2`)

---

## 7. Evaluation Protocol

To keep results comparable across methods, we recommend reporting:

- **ATE (RMSE)** over the full trajectory
- **RPE** at 10 m / 50 m sub-trajectory lengths
- **Final drift** as a percentage of trajectory length, which is the most informative metric in tunnel-like scenes where error accumulates along a single axis

Ground-truth alignment should use `Sim(3)`-free rigid alignment (`evo -a`, not `-s`), since the dataset provides metric-scale references.

---

## 8. License

This dataset is released under **CC BY-NC-SA 4.0**. It may be used for academic research; commercial use requires prior permission.

<!-- Choose a license that matches your intent. If you also ship code in tools/,
     it is common to dual-license: CC BY-NC-SA for data, MIT/Apache-2.0 for code. -->

---

## 9. Citation

If you use this dataset in your research, please cite:

```bibtex
@article{TODO2026geodegen,
  title   = {TODO: Paper Title},
  author  = {TODO},
  journal = {TODO},
  year    = {2026}
}
```

---

## 10. Acknowledgements

TODO — funding, lab, and any site access permissions.

## 11. Contact

Questions and issues are welcome via [GitHub Issues](../../issues), or contact TODO@example.com.
