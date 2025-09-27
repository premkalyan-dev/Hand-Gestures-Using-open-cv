

# ✨ Gesture-Controlled Screen Brightness

This Python project allows you to control your computer screen's brightness using hand gestures captured by your webcam. It utilizes MediaPipe for accurate hand tracking and mapping the distance between your thumb and index finger to the screen brightness level.

## 💡 Principle of Operation

The core logic of the system is based on tracking two key hand landmarks:

1.  **Landmark 4 (Thumb Tip)**
2.  **Landmark 8 (Index Finger Tip)**

The script continuously calculates the Euclidean distance (`L`) between these two points in pixels. This distance is then mapped (interpolated) to the system's brightness range (0 to 100) and applied using the `screen-brightness-control` library.

| Hand Gesture | Action | Brightness Level |
| :--- | :--- | :--- |
| **Pinch Close** (Thumb and Index close) | Distance $L \approx 15$ | Brightness $\approx 0\%$ |
| **Open Spread** (Thumb and Index far) | Distance $L \approx 220$ | Brightness $\approx 100\%$ |

## 🛠️ Prerequisites

To run this script, you need to have Python installed along with the following libraries:

1.  `opencv-python`
2.  `mediapipe`
3.  `numpy`
4.  `screen-brightness-control` (sbc)

### Installation

You can install all necessary dependencies using `pip`:

```bash
pip install opencv-python mediapipe numpy screen-brightness-control
```

> **Note on `screen-brightness-control` (sbc):** This library's functionality can vary depending on your operating system and display hardware. It typically works well on Windows and Linux, but may have limitations on certain setups or macOS.

## 🚀 How to Run

1.  **Save the Code:** Save the provided Python code as a file named `brightness_control.py`.

2.  **Execute the Script:** Open your terminal or command prompt and run the script:

    ```bash
    python brightness_control.py
    ```

3.  **Control Brightness:**

      * A window titled "Image" showing your webcam feed will appear.
      * Hold up your hand and bring the **thumb and index finger together** to **decrease** the screen brightness.
      * **Spread your thumb and index finger apart** to **increase** the screen brightness.

4.  **Exit:** Press the **`q`** key while the video window is active to close the application.

## 💻 Code Structure Highlights

### 1\. Initialization

Initializes the MediaPipe Hands model for tracking up to two hands with moderate complexity and high confidence thresholds.

```python
mpHands = mp.solutions.hands
hands = mpHands.Hands(
    static_image_mode=False,
    model_complexity=1,
    min_detection_confidence=0.75,
    min_tracking_confidence=0.75,
    max_num_hands=2)
```

### 2\. Hand Tracking and Landmark Calculation

The main loop processes the frame, extracts the pixel coordinates for all 21 hand landmarks, and stores them in `landmarkList`.

```python
# ... inside the main loop ...
if Process.multi_hand_landmarks:
    for handlm in Process.multi_hand_landmarks:
        # Loop through landmarks to get pixel coordinates
        x, y = int(landmarks.x*width), int(landmarks.y*height)
        landmarkList.append([_id, x, y])

    Draw.draw_landmarks(frame, handlm, mpHands.HAND_CONNECTIONS)
```

### 3\. Distance and Interpolation

This is the core logic that translates the gesture into a brightness value:

```python
x_1, y_1 = landmarkList[4][1], landmarkList[4][2] # Thumb Tip (ID 4)
x_2, y_2 = landmarkList[8][1], landmarkList[8][2] # Index Tip (ID 8)

# Calculate distance (L) between the two tips
L = hypot(x_2 - x_1, y_2 - y_1)

# Interpolate the distance (L) from a range of [15, 220] pixels
# to the brightness level [0, 100]%
b_level = np.interp(L, [15, 220], [0, 100])

# Set the system brightness
sbc.set_brightness(int(b_level))
```

The visible green line and circles on the thumb and index finger provide real-time visual feedback on the measured distance.
