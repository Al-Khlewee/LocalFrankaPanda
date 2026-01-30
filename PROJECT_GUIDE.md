# FrankaRobot - Comprehensive Project Guide

> **AI-Powered Robotic Pick-and-Place Simulation with Gemini Vision**

---

## 📋 Table of Contents

1. [Project Overview](#project-overview)
2. [Features](#features)
3. [Technology Stack](#technology-stack)
4. [Architecture Overview](#architecture-overview)
5. [Installation & Setup](#installation--setup)
6. [Project Structure](#project-structure)
7. [Core Components Deep Dive](#core-components-deep-dive)
   - [Application Entry Point](#1-application-entry-point)
   - [MuJoCo Simulation Engine](#2-mujoco-simulation-engine)
   - [Render System](#3-render-system)
   - [Inverse Kinematics System](#4-inverse-kinematics-system)
   - [Analytical IK Solver](#5-analytical-ik-solver)
   - [Sequence Animator](#6-sequence-animator)
   - [Robot Loader](#7-robot-loader)
   - [UI Components](#8-ui-components)
8. [How the System Works](#how-the-system-works)
9. [API Integration](#api-integration)
10. [Usage Guide](#usage-guide)
11. [Configuration](#configuration)
12. [Troubleshooting](#troubleshooting)
13. [License](#license)

---

## Project Overview

**FrankaRobot** is an advanced web-based robotics simulation that demonstrates **AI-powered spatial reasoning** for robotic manipulation. The project combines:

- **Real-time physics simulation** using MuJoCo (Multi-Joint dynamics with Contact)
- **3D visualization** powered by Three.js
- **AI vision analysis** through Google's Gemini Robotics Embodied Reasoning (ER) model
- **Analytical Inverse Kinematics** for the Franka Emika Panda robot arm

The application allows users to:
1. View a simulated Franka Panda robot arm in a 3D environment
2. Use AI to detect objects in the scene (cubes, items)
3. Command the robot to autonomously pick up detected objects and stack them

---

## Features

### 🤖 Robot Simulation
- **Franka Emika Panda** 7-DOF robotic arm simulation
- Real-time physics with MuJoCo WASM
- Interactive 3D camera controls (pan, zoom, rotate)
- Pause/resume simulation

### 🧠 AI Vision Integration
- **Gemini Robotics ER 1.5 Preview** model integration
- Three detection modes:
  - **2D Bounding Boxes**: Rectangular regions around objects
  - **Segmentation Masks**: Precise pixel-level boundaries
  - **Points**: Exact coordinate pinpointing
- Configurable temperature and thinking parameters

### 🎮 Interactive Controls
- Click on detected markers to move robot
- Automated pick-and-place sequences
- Speed control (1x, 2x, 5x, 10x, 20x)
- Dark/Light mode toggle
- Responsive design (mobile & desktop)

### 📊 Visualization
- Real-time gizmo showing end-effector position/orientation
- Visual markers for detected objects
- API call history with request/response logs
- Camera flash effect on scene capture

---

## Technology Stack

| Category | Technology |
|----------|------------|
| **Frontend Framework** | React 19.2 |
| **3D Graphics** | Three.js 0.181 |
| **Physics Engine** | MuJoCo-JS 0.0.7 (WebAssembly) |
| **AI/ML** | Google GenAI SDK (@google/genai) |
| **Build Tool** | Vite 6.2 |
| **Language** | TypeScript 5.8 |
| **Icons** | Lucide React |
| **Styling** | Tailwind CSS (inline classes) |
| **UUID** | uuid v13 |

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                         App.tsx (Main)                          │
│  - State Management                                             │
│  - UI Rendering                                                 │
│  - Gemini API Calls                                             │
└───────────────────────────────┬─────────────────────────────────┘
                                │
            ┌───────────────────┼───────────────────┐
            │                   │                   │
            ▼                   ▼                   ▼
┌───────────────────┐ ┌─────────────────┐ ┌─────────────────────┐
│   MujocoSim.ts    │ │  RenderSystem   │ │   UI Components     │
│  (Orchestrator)   │ │   (3D Scene)    │ │ Toolbar/Sidebar     │
└─────────┬─────────┘ └────────┬────────┘ └─────────────────────┘
          │                    │
    ┌─────┴─────┐        ┌─────┴─────┐
    │           │        │           │
    ▼           ▼        ▼           ▼
┌────────┐ ┌────────┐ ┌────────┐ ┌─────────┐
│IkSystem│ │Sequence│ │GeomBld │ │Reflector│
│        │ │Animator│ │        │ │         │
└────┬───┘ └────────┘ └────────┘ └─────────┘
     │
     ▼
┌──────────────────┐
│FrankaAnalyticalIK│
│(7-DOF IK Solver) │
└──────────────────┘
```

### Data Flow

1. **Initialization**: MuJoCo WASM loads → Robot model loaded → Scene initialized
2. **Detection**: User prompts → Camera captures scene → Gemini API analyzes → Results displayed
3. **Manipulation**: User clicks marker → IK solves target pose → SequenceAnimator executes pickup

---

## Installation & Setup

### Prerequisites

- **Node.js** (v18+ recommended)
- **npm** or **yarn**
- **Gemini API Key** (from Google AI Studio)

### Step-by-Step Installation

```bash
# 1. Clone or download the project
cd Frankrobot--main

# 2. Install dependencies
npm install

# 3. Configure API Key
# Edit .env.local and set your Gemini API key:
# GEMINI_API_KEY=your_api_key_here

# 4. Start development server
npm run dev

# 5. Open browser
# Navigate to http://localhost:5173
```

### Available Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start development server with hot reload |
| `npm run build` | Build for production |
| `npm run preview` | Preview production build |

---

## Project Structure

```
Frankrobot--main/
├── 📄 index.html           # HTML entry point
├── 📄 index.tsx            # React entry point
├── 📄 index.css            # Global styles (Tailwind)
├── 📄 App.tsx              # Main application component
├── 📄 types.ts             # TypeScript type definitions
│
├── 🔧 Core Simulation
│   ├── MujocoSim.ts        # Central orchestrator
│   ├── IkSystem.ts         # Inverse Kinematics controller
│   ├── FrankaAnalyticalIK.ts # Analytical IK solver
│   ├── SequenceAnimator.ts # Pick-and-place state machine
│   └── RobotLoader.ts      # Robot model loader
│
├── 🎨 Rendering
│   ├── RenderSystem.ts     # Three.js scene manager
│   ├── Reflector.ts        # Reflective surfaces
│   └── rendering/
│       └── GeomBuilder.ts  # MuJoCo → Three.js geometry converter
│
├── 🖱️ Interaction
│   ├── DragStateManager.ts # Mouse/touch interaction
│   └── SelectionManager.ts # Object selection
│
├── 🧮 Math Utilities
│   ├── MatMath.ts          # Matrix mathematics
│   └── CapsuleGeometry.ts  # Custom Three.js geometry
│
├── 🎛️ UI Components
│   └── components/
│       ├── Toolbar.tsx     # Bottom control bar
│       ├── UnifiedSidebar.tsx # AI control panel
│       └── RobotSelector.tsx  # Robot info display
│
├── 🛠️ Utilities
│   └── utils/
│       └── StringUtils.ts  # String helper functions
│
├── ⚙️ Configuration
│   ├── package.json        # Dependencies & scripts
│   ├── tsconfig.json       # TypeScript configuration
│   ├── vite.config.ts      # Vite build configuration
│   └── .env.local          # API keys (not committed)
│
└── 📚 Documentation
    ├── README.md           # Quick start guide
    └── Code.md             # Code documentation
```

---

## Core Components Deep Dive

### 1. Application Entry Point

**File**: `App.tsx`

The main React component that orchestrates the entire application.

#### Key Responsibilities:
- **State Management**: Loading states, UI visibility, detection results
- **MuJoCo Initialization**: Loads WASM module asynchronously
- **Gemini API Integration**: Sends detection requests, processes responses
- **Event Handling**: User interactions, camera movements

#### Important State Variables:
```typescript
const [isLoading, setIsLoading] = useState(true);      // Initial loading
const [isPaused, setIsPaused] = useState(false);       // Simulation pause
const [showSidebar, setShowSidebar] = useState(true);  // UI visibility
const [isDarkMode, setIsDarkMode] = useState(false);   // Theme
const [logs, setLogs] = useState<LogEntry[]>([]);      // API call history
```

#### Key Functions:
| Function | Purpose |
|----------|---------|
| `handleErSend()` | Captures scene, calls Gemini API, processes results |
| `handlePickup()` | Initiates pick-and-place sequence |
| `handleReset()` | Resets simulation to initial state |

---

### 2. MuJoCo Simulation Engine

**File**: `MujocoSim.ts`

The central orchestrator connecting MuJoCo physics to Three.js visualization.

#### Class Structure:
```typescript
export class MujocoSim {
    mujoco: MujocoModule;           // WASM module reference
    mjModel: MujocoModel | null;    // Physics model
    mjData: MujocoData | null;      // Simulation state
    
    renderSys: RenderSystem;        // 3D rendering
    ikSys: IkSystem;                // Inverse kinematics
    sequenceAnimator: SequenceAnimator; // Automation
    
    paused: boolean;
    speedMultiplier: number;
}
```

#### Main Loop:
```typescript
private startLoop() {
    const loop = () => {
        // 1. Handle drag interactions
        this.dragStateManager.update();
        
        // 2. Animate gizmo if moving
        if (this.gizmoAnim.active) { /* interpolate position */ }
        
        // 3. Run physics (if not paused)
        if (!this.paused) {
            if (this.sequenceAnimator.running) {
                this.sequenceAnimator.update(...);
            } else {
                this.ikSys.update(this.mjModel, this.mjData);
            }
            // Step simulation at speed
            while (this.mjData.time - startSimTime < (1/60) * this.speedMultiplier) {
                this.mujoco.mj_step(this.mjModel, this.mjData);
            }
        }
        
        // 4. Render frame
        this.renderSys.update(this.mjData, showContacts);
        requestAnimationFrame(loop);
    };
}
```

#### Key Methods:
| Method | Description |
|--------|-------------|
| `init(robotId, sceneFile)` | Loads robot and initializes simulation |
| `moveIkTargetTo(pos, duration)` | Animates IK target to position |
| `pickupItems(positions, markerIds)` | Starts automated pickup sequence |
| `reset()` | Resets simulation, randomizes cubes |
| `togglePause()` | Pauses/resumes physics |
| `dispose()` | Cleanup resources |

---

### 3. Render System

**File**: `RenderSystem.ts`

Manages all Three.js 3D rendering operations.

#### Scene Structure:
```
Scene
├── simGroup (Simulation objects)
│   ├── bodies[] (Robot parts, cubes, floor)
│   ├── contactMarkers (Debug: collision points)
│   ├── Lights (Directional, Ambient)
│   └── IK Target (Axes helper)
├── erGroup (Detection markers)
└── Grid (Ground plane grid)
```

#### Camera Setup:
```typescript
camera = new THREE.PerspectiveCamera(45, aspect, 0.1, 1000);
camera.up.set(0, 0, 1);  // Z-up coordinate system
camera.position.set(2, -1.5, 2.5);
```

#### Key Features:
- **Smooth Camera Animation**: `moveCameraTo(position, target, duration)`
- **Dark Mode Support**: `setDarkMode(enabled)`
- **Scene Snapshots**: `getCanvasSnapshot(width, height, mimeType)`
- **2D→3D Projection**: `project2DTo3D(x, y, cameraPos, lookAt)`
- **ER Markers**: `addErMarker(position, label, id)`

---

### 4. Inverse Kinematics System

**File**: `IkSystem.ts`

Controls the robot's end-effector positioning using inverse kinematics.

#### Components:
- **target**: `THREE.Group` - Visual representation of desired pose
- **control**: `TransformControls` - Interactive gizmo for manual control

#### IK Solving Strategy:
```typescript
solve(pos: Vector3, quat: Quaternion, currentQ: number[]): number[] | null {
    // 1. Try current q7 (joint 7) for continuity
    processCandidateQ7(currentQ7);
    
    // 2. Search nearby q7 values (±0.5 rad)
    for (let q7 = currentQ7 - 0.5; q7 <= currentQ7 + 0.5; q7 += 0.1) {
        processCandidateQ7(q7);
    }
    
    // 3. Fallback: Full range search
    if (!bestSolution) {
        for (let q7 = -2.89; q7 <= 2.89; q7 += 0.2) {
            processCandidateQ7(q7);
        }
    }
    
    return bestSolution;
}
```

#### Redundancy Resolution:
The Franka Panda has 7 joints but only 6 DOF for end-effector pose. The 7th joint (q7) provides redundancy, which is resolved by:
1. Maintaining continuity with current configuration
2. Minimizing distance to neutral "home" pose
3. Cost function: `α × dist_current + β × dist_neutral`

---

### 5. Analytical IK Solver

**File**: `FrankaAnalyticalIK.ts`

Implements closed-form inverse kinematics based on the research paper:
> "Analytical Inverse Kinematics for Franka Emika Panda – a Geometrical Solver for 7-DOF Manipulators with Unconventional Design" by Yanhao He and Steven Liu

#### Robot Parameters (DH):
```typescript
const d1 = 0.333;   // Link 1 length
const d3 = 0.316;   // Link 3 length
const d5 = 0.384;   // Link 5 length
const dF = 0.107;   // Flange offset
const a4 = 0.0825;  // Joint 4 offset
const a5 = -0.0825; // Joint 5 offset
const a7 = 0.088;   // Joint 7 offset
const dEE = 0.10;   // End-effector offset
```

#### Joint Limits (radians):
```typescript
const Q_MIN = [-2.8973, -1.7628, -2.8973, -3.0718, -2.8973, -0.0175, -2.8973];
const Q_MAX = [ 2.8973,  1.7628,  2.8973, -0.0698,  2.8973,  3.7525,  2.8973];
```

#### Algorithm Steps:
1. **Calculate Wrist Center (p7)**: `p7 = pEE - (dF + dEE) × zEE`
2. **Calculate Frame 6 Origin (p6)**: Using q7 and geometric relations
3. **Solve q4 (Elbow)**: Law of cosines on triangle O2-O4-O6
4. **Solve q6 (Wrist)**: Two candidate solutions (B1, B2)
5. **Solve q1, q2 (Shoulder)**: Two configurations (C1, C2)
6. **Solve q3 (Elbow rotation)**: Frame transformation
7. **Solve q5 (Wrist flip)**: Geometric projection

---

### 6. Sequence Animator

**File**: `SequenceAnimator.ts`

State machine for automated pick-and-place operations.

#### Pickup Sequence (15 Steps):
| Step | Name | Duration | Action |
|------|------|----------|--------|
| 0 | Move over Cube | 0.6s | Position above target |
| 1 | Hover | 0.2s | Stabilize |
| 2 | Open | 0.3s | Open gripper |
| 3 | Lower | 0.4s | Descend to grasp height |
| 4 | Wait | 0.2s | Stabilize |
| 5 | Grasp | 0.3s | Close gripper |
| 6 | Wait | 0.2s | Secure grip |
| 7 | Lift | 0.5s | Raise object |
| 8 | Move to Tray | 0.7s | Navigate to drop zone |
| 9 | Lower | 0.4s | Descend to place height |
| 10 | Wait | 0.2s | Stabilize |
| 11 | Release | 0.3s | Open gripper |
| 12 | Wait | 0.2s | Clear object |
| 13 | Lift | 0.4s | Raise |
| 14 | Return Home | 0.5s | Return to neutral |

#### Interpolation:
```typescript
// Joint Space Interpolation (smooth motion)
for (let i = 0; i < 7; i++) {
    mjData.ctrl[i] = startJoints[i] + (targetJoints[i] - startJoints[i]) * ease;
}

// Cylindrical Interpolation for large moves (avoids collisions)
const r = startR + (targetR - startR) * ease;
const theta = startTheta + angleDiff * ease;
const z = startZ + (targetZ - startZ) * ease;
```

---

### 7. Robot Loader

**File**: `RobotLoader.ts`

Handles downloading and preparing robot models from DeepMind's MuJoCo Menagerie.

#### Process:
1. **Clean Virtual FS**: Unmount previous `/working` directory
2. **Queue Processing**: BFS through XML dependencies
3. **Download Assets**: XMLs, STL meshes, textures
4. **Patch Scene**: Inject demo objects (cubes, trays)
5. **Write to VFS**: Store in MuJoCo's virtual filesystem

#### Robot Source:
```
https://raw.githubusercontent.com/google-deepmind/mujoco_menagerie/main/franka_emika_panda/
```

#### Scene Patching:
```typescript
// Inject 20 colored cubes for stacking demo
const colors = ['Red', 'Cyan', 'Green', 'Yellow'];
for (let i = 0; i < 20; i++) {
    // Generate random position (polar coordinates)
    // Avoid robot base and stacking area
    injection += `<body name="cube${i}" pos="${x} ${y} 0.02">...`;
}
```

---

### 8. UI Components

#### Toolbar (`components/Toolbar.tsx`)
Floating control bar with:
- ▶️/⏸️ Play/Pause button
- 🔄 Reset button
- 🌙/☀️ Dark mode toggle
- 📊 Sidebar toggle

#### UnifiedSidebar (`components/UnifiedSidebar.tsx`)
Main control panel featuring:
- **Detection Mode Selector**: Boxes/Masks/Points
- **Prompt Input**: Describe what to detect
- **Settings**: Temperature, Thinking toggle
- **Pickup Button**: Execute detection + pickup
- **History Log**: Previous API calls with results

#### RobotSelector (`components/RobotSelector.tsx`)
Info overlay showing:
- Current robot name
- End-effector position (X, Y, Z)
- End-effector rotation (X, Y, Z)

---

## How the System Works

### Complete Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│ 1. INITIALIZATION                                                │
├──────────────────────────────────────────────────────────────────┤
│ User opens app                                                   │
│     ↓                                                            │
│ Load MuJoCo WASM module                                          │
│     ↓                                                            │
│ Download robot XMLs & meshes from GitHub                         │
│     ↓                                                            │
│ Initialize MuJoCo model & data                                   │
│     ↓                                                            │
│ Create Three.js scene with robot bodies                          │
│     ↓                                                            │
│ Start render loop (60 FPS)                                       │
└──────────────────────────────────────────────────────────────────┘
                              ↓
┌──────────────────────────────────────────────────────────────────┐
│ 2. DETECTION (User clicks "Detect & Pickup")                     │
├──────────────────────────────────────────────────────────────────┤
│ Animate camera to top-down view                                  │
│     ↓                                                            │
│ Capture scene as PNG (max 640×640)                               │
│     ↓                                                            │
│ Build prompt: "Detect [user input] items..."                     │
│     ↓                                                            │
│ Call Gemini API (gemini-robotics-er-1.5-preview)                 │
│     ↓                                                            │
│ Parse JSON response (boxes/points/masks)                         │
│     ↓                                                            │
│ Project 2D coordinates to 3D world positions                     │
│     ↓                                                            │
│ Add visual markers at detected positions                         │
└──────────────────────────────────────────────────────────────────┘
                              ↓
┌──────────────────────────────────────────────────────────────────┐
│ 3. MANIPULATION (Automatic pickup)                               │
├──────────────────────────────────────────────────────────────────┤
│ SequenceAnimator receives target positions                       │
│     ↓                                                            │
│ For each target:                                                 │
│   ├── Solve IK for approach pose                                 │
│   ├── Interpolate joints to position above target                │
│   ├── Lower & close gripper                                      │
│   ├── Solve IK for stack position                                │
│   ├── Navigate to drop zone                                      │
│   ├── Lower & release                                            │
│   └── Return home                                                │
│     ↓                                                            │
│ Remove marker, increment stack counter                           │
│     ↓                                                            │
│ Repeat for next target                                           │
└──────────────────────────────────────────────────────────────────┘
```

---

## API Integration

### Gemini Robotics ER Model

**Model ID**: `gemini-robotics-er-1.5-preview`

This specialized model is designed for embodied reasoning in robotics applications.

#### Request Format:
```typescript
const response = await ai.models.generateContent({
    model: 'gemini-robotics-er-1.5-preview',
    contents: {
        parts: [
            { inlineData: { mimeType: 'image/png', data: base64Image } },
            { text: 'Detect red cubes, with no more than 25 items...' }
        ]
    },
    config: {
        temperature: 0.1,
        responseMimeType: "application/json",
        thinkingConfig: { thinkingBudget: 0 }  // Optional: disable thinking
    }
});
```

#### Response Format (2D Bounding Boxes):
```json
[
    {
        "box_2d": [ymin, xmin, ymax, xmax],  // Normalized 0-1000
        "label": "red cube"
    },
    ...
]
```

#### Response Format (Points):
```json
[
    {
        "point": [y, x],  // Normalized 0-1000
        "label": "red cube center"
    },
    ...
]
```

---

## Usage Guide

### Basic Operations

#### 1. Start Simulation
- Application loads automatically
- Wait for "Initializing Spatial Engine..." to complete
- Robot appears in initial pose

#### 2. Camera Controls
- **Rotate**: Left-click + drag
- **Pan**: Right-click + drag (or two-finger drag)
- **Zoom**: Scroll wheel (or pinch)

#### 3. Detect Objects
1. Open sidebar (panel icon in toolbar)
2. Select detection mode (Boxes/Masks/Points)
3. Enter prompt (e.g., "red cubes")
4. Adjust temperature if needed (lower = more precise)
5. Toggle thinking on/off
6. Click **"Detect & Pickup"**

#### 4. Manual Pickup
- After detection, click on any purple marker
- Robot will move to that position
- Use the transform gizmo to fine-tune

#### 5. Speed Control
- During pickup sequence, click the speed button
- Cycles through: 1x → 2x → 5x → 10x → 20x → 2x

#### 6. Reset
- Click reset button (🔄) to:
  - Reset robot to home position
  - Randomize cube positions
  - Clear detection history

---

## Configuration

### Environment Variables

Create `.env.local` in project root:
```env
GEMINI_API_KEY=your_api_key_here
```

### TypeScript Configuration (`tsconfig.json`)
```json
{
    "compilerOptions": {
        "target": "ES2022",
        "module": "ESNext",
        "strict": true,
        "jsx": "react-jsx"
    }
}
```

### Vite Configuration (`vite.config.ts`)
```typescript
export default defineConfig({
    plugins: [react()],
    define: {
        'process.env.API_KEY': JSON.stringify(process.env.GEMINI_API_KEY)
    }
});
```

---

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| **WASM failed to load** | Reload page; check internet connection |
| **API key error** | Verify `.env.local` contains valid key |
| **Robot doesn't move** | Check if simulation is paused |
| **No detection results** | Try different prompt; adjust temperature |
| **IK fails** | Target may be unreachable; try closer position |
| **Performance issues** | Close other tabs; reduce browser extensions |

### Debug Tips

1. **Check Console**: Open browser DevTools (F12) → Console
2. **Physics State**: Watch `mjData.time` advancing
3. **IK Solutions**: Monitor `ikSys.calculating` state
4. **API Responses**: Expand logs in sidebar

---

## License

This project is licensed under the **Apache License 2.0**.

```
Copyright 2024 Google LLC

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

---

## Additional Resources

- [MuJoCo Documentation](https://mujoco.readthedocs.io/)
- [Three.js Documentation](https://threejs.org/docs/)
- [Google AI Studio](https://ai.studio.google/)
- [Franka Emika Panda Specs](https://frankaemika.github.io/docs/)
- [MuJoCo Menagerie (Robot Models)](https://github.com/google-deepmind/mujoco_menagerie)

---

*Created with ❤️ for the robotics and AI community*
