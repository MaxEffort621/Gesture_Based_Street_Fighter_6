# Gesture Gaming — Webcam-Based Hands-Free Control for Street Fighter 6

A webcam-based gesture controller for **Street Fighter 6** using Python, OpenCV, MediaPipe Pose, and pynput. The system detects body/hand-related pose landmarks from a webcam and converts configured gestures into keyboard inputs for hands-free gameplay.

## Overview

Gesture Gaming explores an alternative human-computer interaction method where a standard webcam is used as the input device. Instead of relying entirely on a conventional keyboard or controller, the system tracks selected body landmarks and maps their positions to keyboard commands.

The project is implemented in Python and uses:

- **OpenCV** for webcam capture and image processing
- **MediaPipe Pose** for real-time pose landmark detection
- **pynput** for keyboard input simulation
- **Python virtual environment** for dependency isolation

The system was configured and demonstrated for **Street Fighter 6** using a predefined keyboard mapping.

## Features

- Real-time webcam-based pose detection
- Gesture/landmark-based action triggering
- Configurable keyboard mappings
- Continuous left/right movement
- Cooldown control for discrete actions
- Compatible with standard webcam hardware
- Modular Python project structure
- Designed for hands-free fighting-game interaction

## Technologies Used

| Technology | Purpose |
|---|---|
| Python 3.12.2 | Core programming language |
| OpenCV | Webcam capture and image processing |
| MediaPipe Pose 0.10.21 | Pose landmark detection |
| pynput | Keyboard input simulation |
| imutils | Image-processing utilities |
| PyYAML | Configuration support |

## Project Structure

```text
Gesture-Gaming/
│
├── core/
│   ├── input.py
│   └── settings.py
│
├── pose/
│   ├── pose.py
│   ├── setup.py
│   └── utils.py
│
├── track/
│   ├── tracking.py
│   ├── motion_detector.py
│   ├── setup.py
│   └── utils.py
│
├── key_config.json
├── requirements.txt
└── README.md
```

The **pose** module is the primary implementation used for the Street Fighter 6 demonstration. The tracking module contains additional motion-tracking functionality but is not required for the SF6 demonstration.

## Installation

### 1. Clone the repository

```bash
git clone <YOUR-GITHUB-REPOSITORY-URL>
cd Gesture-Gaming
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

### 3. Activate the virtual environment

**Windows:**

```bash
.venv\Scripts\activate
```

**Linux/macOS:**

```bash
source .venv/bin/activate
```

### 4. Install dependencies

The demonstrated configuration used:

```bash
pip install mediapipe==0.10.21
pip install opencv-contrib-python==4.11.0.86
pip install pynput
pip install imutils
pip install pyyaml
```

Alternatively, if a `requirements.txt` file is provided:

```bash
pip install -r requirements.txt
```

## Running the Project

### Start the Pose Controller

```bash
python -m pose.pose
```

This starts webcam-based pose detection and the gesture-control pipeline.

### Configure the Controller

```bash
python -m pose.setup
```

The setup module is used to configure trigger zones and associated actions.

Make sure the webcam is connected and accessible through the configured camera index.

## Street Fighter 6 Controls

The following keyboard configuration was used for the demonstration:

| Gesture / Action | Configured Key | SF6 Function |
|---|---:|---|
| Move Left | `A` | Left |
| Move Right | `D` | Right |
| Move Up | `W` | Up |
| Move Down | `S` | Down |
| Punch | `U` | Punch |
| Kick | `J` | Kick |
| Kick 2 | `K` | Kick 2 |
| Block | `O` | Block |
| Grab | `H` | Grab |

> **Note:** The `H`/Grab action remains an open item in the demonstrated implementation because a dedicated landmark check was not wired to this action.

## How It Works

### 1. Camera and Pose Detection

The system captures frames from the webcam using OpenCV:

```python
cv2.VideoCapture(0)
```

Frames are converted from BGR to RGB before being passed to MediaPipe Pose.

MediaPipe provides normalized body landmarks that are used by the controller to determine the user's position and trigger actions.

### 2. Trigger-Zone Actions

A rectangular trigger zone is defined relative to the detected face/nose position.

Selected landmarks, including hand and knee landmarks, are checked against this zone. When a landmark enters the corresponding region, the associated action is triggered.

### 3. Action-to-Key Mapping

Actions are connected to keyboard keys through `key_config.json`.

The controller loads the configured mapping and uses `pynput` to simulate key presses and releases.

Example:

```text
Punch  -> U
Kick   -> J
Block  -> O
```

### 4. Continuous Movement

Continuous movement is handled using the shoulder center and a reference position.

The system compares the current shoulder-center position with the reference position and holds or releases the appropriate movement key depending on the detected displacement.

### 5. Cooldown and False-Trigger Control

A cooldown interval is used for discrete actions to prevent the same gesture from generating repeated keyboard events in rapid succession.

This helps reduce accidental repeated inputs caused by continuous landmark detection.

## Landmark Mapping Used

The demonstrated configuration used the following landmark-to-action relationships:

| Detected Landmark | Internal Action | Output |
|---|---|---|
| Right index / `left_hand` | Punch | `U` |
| Left index / `right_hand` | Block | `O` |
| Left knee | Kick 2 | `K` |
| Right knee | Kick | `J` |

The naming of some internal variables follows the original project structure and does not necessarily correspond directly to the user's physical left/right side.

## Testing

The implemented controller was tested using functional checks for:

| Test | Result |
|---|---|
| Environment and dependency setup | Passed |
| Pose controller execution | Passed |
| Punch input | Passed |
| Block input | Passed |
| Continuous movement | Passed |
| Keyboard output | Passed |
| Grab input | Open item |

These tests verify that the corresponding software components execute and produce the expected configured keyboard events.

## Empirical Evaluation

A separate companion gesture-controller experiment was conducted using **500 trials** across 10 gesture classes.

| Metric | Result |
|---|---:|
| Total trials | 500 |
| Successful detections | 484 |
| Unsuccessful detections | 16 |
| Aggregate accuracy | 96.8% |
| Mean response time | 49 ms |

**Important:** These measurements belong to the companion FPS/RPG gesture-controller experiment and should **not be interpreted as measured Street Fighter 6 accuracy**. The SF6 implementation was evaluated through functional tests and demonstration rather than the same 500-trial quantitative protocol.

## Hardware Requirements

The project is designed to work with a standard webcam-based setup.

Recommended hardware:

- Laptop or desktop computer
- Built-in or USB webcam
- Keyboard
- Sufficient lighting for pose detection
- Street Fighter 6 installation for gameplay testing

A dedicated depth camera or external motion-tracking device is not required for the demonstrated implementation.

## Configuration

Keyboard mappings are stored in:

```text
key_config.json
```

The mapping can be modified to match the keyboard configuration used by the game.

For example:

```json
{
    "left": "a",
    "right": "d",
    "up": "w",
    "down": "s",
    "punch": "u",
    "kick": "j",
    "kick2": "k",
    "block": "o",
    "grab": "h"
}
```

Use the exact key names expected by the project's input-handling implementation.

## Limitations

- Trigger zones are based on landmark positions rather than semantic hand-sign recognition.
- Detection can be affected by lighting conditions, camera placement, and body/landmark occlusion.
- Cooldown values can affect responsiveness and false-trigger behavior.
- The game must have the correct keyboard focus for simulated inputs to be received.
- The current implementation does not provide a dedicated landmark condition for the Grab action.
- The demonstrated SF6 evaluation is primarily functional rather than a large-scale quantitative accuracy study.
- The project uses MediaPipe Pose rather than a dedicated hand-landmark model.

## Future Work

Possible improvements include:

- MediaPipe Hands with 21 hand landmarks
- Gesture normalization
- SVM, Random Forest, or lightweight neural-network classification
- Temporal smoothing
- Confidence thresholds
- Per-action cooldown values
- Dedicated Grab gesture detection
- Repeated-trial quantitative evaluation for SF6
- Gesture-sequence and combo recognition
- More robust handling of occlusion and lighting variations

## Demonstration

Add the project demonstration video here:

```text
[Demo Video Link]
```

If the video is hosted on Google Drive, GitHub, YouTube, or another platform, replace the placeholder with the corresponding link.

## Team

**Team Agamotto**

- Hariom Patidar — 23BCE1268
- Gaurav Singh — 23BCE1299
- Abhishek Jadli — 23BCE5133

## GitHub Topics

Recommended repository topics:

```text
computer-vision
gesture-recognition
mediapipe
opencv
python
human-computer-interaction
gesture-control
gaming
street-fighter-6
pose-estimation
```

## References

The project is based on computer-vision, pose-estimation, and gesture-interaction techniques using OpenCV, MediaPipe, and related research literature.

## License

Add the project's license here if applicable.

Example:

```text
MIT License
```

If no license has been selected, remove this section until one is added.
