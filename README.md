# Ahthisham-26-A14-008
# UAV-Guided Rover Triage Navigation

Welcome to the **UAV-Guided Rover Triage Navigation System** repository! This project implements an end-to-end computer vision and path-planning pipeline built for mass casualty response scenarios. Guided by aerial segmentation maps provided by a UAV, a ground rover navigates through complex terrain, avoids non-traversable obstacles, prioritizes casualties based on age and condition severity, and calculates optimal rescue routes.

---

## Table of Contents
- [Project Overview](#project-overview)
- [Key Features](#key-features)
- [5-Day Development Journey](#5-day-development-journey)
  - [Day 1: Environment Setup & Fundamentals](#day-1-environment-setup--fundamentals)
  - [Day 2: Computer Vision & Terrain Masking](#day-2-computer-vision--terrain-masking)
  - [Day 3: Casualty Detection & Scoring](#day-3-casualty-detection--scoring)
  - [Day 4: Pathfinding & Obstacle Avoidance](#day-4-pathfinding--obstacle-avoidance)
  - [Day 5: Visualization, Ranking & Optimization](#day-5-visualization-ranking--optimization)
- [Technical Architecture & Scoring Rules](#technical-architecture--scoring-rules)
- [Error Analysis & Performance Optimizations](#error-analysis--performance-optimizations)
- [Installation & Getting Started](#installation--getting-started)
- [Usage](#usage)

---

## Project Overview

In a disaster scenario, rapid decision-making saves lives. This system uses aerial imagery captured by a UAV to create a detailed segmentation map of the triage site:
1. **Obstacle Detection**: Identifies non-traversable barriers (black regions).
2. **Terrain Profiling**: Recognizes three distinct ground elevation levels (light, medium, and dark green), each affecting rover speed.
3. **Casualty Triage**: Locates victims using shape and color recognition to determine age group and severity.
4. **Route Optimization**: Computes paths starting at the launch zone (orange triangle), visiting casualties, and ending at the safe zone (purple triangle) to maximize total score while minimizing travel time.

---

## Key Features
- **HSV-based Segmentation**: Robust color filtering isolated from lighting variance.
- **Automated Triage Matrix**: Multi-factor scoring based on age and severity.
- **Terrain-Aware Pathfinding**: Speed-adjusted travel modeling across flat and elevated grounds.
- **Obstacle Avoidance**: Dynamic A* pathfinding around solid boundaries.
- **Leaderboard Generator**: Global ranking of multi-image runs by score and completion time.

---

## 5-Day Development Journey

### Day 1: Environment Setup & Fundamentals
- **What Was Done**: Set up the Linux/Ubuntu execution environment, configured Python 3.10 virtual environments, and initialized the GitHub project layout.
- **Key Learnings**: Mastered basic OpenCV matrix manipulation, image reading/writing, and structured Python data collections (dictionaries, tuple coordinates).
- **Problems Encountered**: Path resolution conflicts and missing C++ backend dependencies for OpenCV/NumPy.
- **Solutions & Fixes**: Isolated dependencies within a dedicated Python `venv` and built explicit module loading routines.

### Day 2: Computer Vision & Terrain Masking
- **What Was Done**: Developed binary thresholding scripts to generate traversable vs. non-traversable region masks.
- **Key Learnings**: Transitioned from standard RGB color spaces to HSV (Hue, Saturation, Value) color spaces for clear isolation of subtle shade differences.
- **Problems Encountered**: Standard RGB thresholding failed to separate the three shades of green terrain reliably under slight hue shifts.
- **Solutions & Fixes**: Fine-tuned HSV range boundaries with custom hue/saturation masks to isolate each terrain tier cleanly.

### Day 3: Casualty Detection & Scoring
- **What Was Done**: Built shape recognition and color extraction pipelines using contour analysis to classify victim age and severity.
- **Key Learnings**: Applied `cv2.approxPolyDP()` to categorize geometry (circles, stars, squares) and mapped severity levels (red, yellow, white).
- **Problems Encountered**: Minor image noise artifacts and pixel specks were detected as valid casualties, corrupting patient count.
- **Solutions & Fixes**: Implemented contour area filtering (`cv2.contourArea`) to discard any shapes below minimum spatial bounds.

### Day 4: Pathfinding & Obstacle Avoidance
- **What Was Done**: Implemented route searching algorithms connecting the start point (orange triangle), victims, and the final safe zone (purple triangle).
- **Key Learnings**: Combined variable ground speeds ($20	ext{ px/s}$, $15	ext{ px/s}$, $10	ext{ px/s}$) with geometric travel distance to measure true travel time ($Time = 	ext{Distance} / 	ext{Speed}$).
- **Problems Encountered**: Basic direct-line connections clipped through non-traversable black wall regions.
- **Solutions & Fixes**: Integrated grid-based A* graph search over the binary obstacle mask to strictly prevent wall collisions.

### Day 5: Visualization, Ranking & Optimization
- **What Was Done**: Rendered final path routes over input images, exported coordinate lists, and built automated global ranking engines for multi-image processing.
- **Key Learnings**: Managed batch dataset outputs and structured global leaderboard sorting algorithms.
- **Problems Encountered**: Plotting dense pixel-by-pixel path lines severely lagged image rendering output speeds.
- **Solutions & Fixes**: Downsampled visual waypoint paths for real-time rendering while retaining high-precision pixel math for accurate distance metrics.

---

## Technical Architecture & Scoring Rules

### Casualty Matrix
| Shape | Age Group | Age Score | Color | Condition | Severity Score |
| :--- | :--- | :---: | :--- | :--- | :---: |
| **Circle** | Children | 3 | **Red** | Critical | 3 |
| **Square** | Senior Citizens | 2 | **Yellow** | Moderate | 2 |
| **Star** | Adults | 1 | **White** | Safe | 1 |

