# DegLoc-Dataset

A Livox Avia LiDAR-inertial dataset collected in **geometrically degenerate indoor/outdoor scenes** — long corridors, open halls, staircases, and large flat squares/playgrounds — where point-to-plane / point-to-edge constraints become weak or ill-conditioned and LiDAR(-inertial) odometry is prone to drift.

---

## 1. Overview

Most LiDAR SLAM datasets are recorded in structurally rich environments. This dataset instead targets scenes where geometric constraints are **insufficient along one or more directions**, e.g. long straight corridors (translation along the corridor axis is weakly constrained), open squares/playgrounds (few vertical structures), and staircases (repetitive, near-symmetric geometry). It is intended for evaluating and improving the robustness of LiDAR-inertial odometry (LIO) under geometric degeneracy.

- **Sensor**: Livox Avia (non-repetitive scan LiDAR) with built-in IMU
- **Format**: ROS 1 bag files (`.bag`)
- **Ground truth**: **not included** — the bags contain raw LiDAR + IMU data only, no motion-capture/RTK/total-station reference trajectory. Except for `corridor1`/`corridor2`, every sequence starts and ends at the same physical location, so localization accuracy is best evaluated with **loop-closure error** (start-to-end pose drift) rather than a full-trajectory metric.
- **Total size**: ~13 GB across 24 sequences

---

## 2. Sensor setup

| Topic | Type | Approx. rate | Content |
|---|---|---|---|
| `/livox/lidar` | `livox_ros_driver/CustomMsg` | ~10 Hz | per-point timestamped point cloud (Livox custom message, not `PointCloud2`) |
| `/livox/imu` | `sensor_msgs/Imu` | ~200 Hz | built-in IMU |

Playing or parsing the bags requires the [`livox_ros_driver`](https://github.com/Livox-SDK/livox_ros_driver) message definitions on the ROS side. Convert to `sensor_msgs/PointCloud2` if your pipeline needs the standard message type.

---

## 3. Sequences

| Scene | Files | Description |
|---|---|---|
| Corridor | `corridor1.bag`, `corridor2.bag`, `corridor_loop1.bag`, `corridor_loop2.bag` | Long, feature-poor indoor corridor; `_loop` sequences walk out and back for drift/loop evaluation |
| Hall | `hall1.bag` – `hall3.bag` | Large indoor hall/lobby, sparse geometric features |
| Lab | `lab1.bag` – `lab4.bag` (+ `lab.png` reference photo) | Indoor lab room, smaller-scale structured scene |
| Playground | `playground3.bag` – `playground7.bag` | Outdoor open playground, large flat area with few vertical structures |
| Square | `square1.bag` – `square6.bag` (+ `square_pic1-3.png` reference photos) | Outdoor open square/plaza |
| Stairs | `stairs1.bag`, `stairs2.bag`, `stairs3.bag` | Staircase ascent/descent, three LiDAR-pitch strategies: **1** level throughout, **2** tilted down descending / tilted up ascending, **3** tilted up descending / tilted up ascending |

File numbering is per scene as recorded and not necessarily continuous (e.g. playground starts at 3).

`corridor1` and `corridor2` are one-way traversals (no return to the start). Every other sequence is a closed loop — start and end pose coincide.

---

## 4. Download

Full dataset (bags + reference photos), hosted on OneDrive:

**[Download link](https://1drv.ms/f/c/79a81de325c140b7/IgA-4FqeGawxQKA-Z424fOaMAeaS9Tpn-0dSFbS094IArCg?e=bF0fVX)**

Total size ≈ 13 GB. The raw data is not stored in this Git repository.

---

## 5. Quick start

```bash
rosbag info corridor1.bag
rosbag play corridor1.bag --clock
```

---

## 6. TODO

- [ ] Benchmark this dataset against common LIO algorithms (e.g. FAST-LIO2, Point-LIO, LIO-SAM) and report loop-closure error per sequence.

---

## 7. Related datasets

Other public datasets targeting geometrically degenerate environments for LiDAR(-inertial) SLAM:

| Dataset | Link |
|---|---|
| GEODE dataset | https://github.com/PengYu-Team/GEODE_dataset |
| ENWIDE dataset | https://projects.asl.ethz.ch/datasets/enwide/ |
| lidar_degeneracy_datasets | https://github.com/ntnu-arl/lidar_degeneracy_datasets |
| unified_autonomy_stack_datasets | https://huggingface.co/datasets/ntnu-arl/unified_autonomy_stack_datasets |
| CERBERUS DARPA Subterranean Challenge – Final Event | https://www.research-collection.ethz.ch/entities/researchdata/5e6105d7-95b1-4e68-8238-9cf3040e0fb9 |
| ICCV'23 Challenge on LiDAR-Inertial Odometry | https://superodometry.com/iccv23_challenge_LiI |
| Newer College Dataset | https://drive.google.com/drive/u/0/folders/1uR476FzjN3PfAiCknVKtuZi3_QfVvSdA |

---

## Contact

Questions or issues: please open a [GitHub issue](../../issues) on this repository.
