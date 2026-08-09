# Neuro-Nod Elite V10.3.1

**Universal Driver Monitoring System & Adaptive Safety Engine**

AI-powered drowsiness and fatigue detection that runs entirely in the browser using real-time 3D computer vision — no cloud, no install, no data upload. Safety, moved from expensive dedicated hardware to the "Edge": your existing phone or laptop browser.

🌐 **Live site:** [upaharmishra.com.np](https://upaharmishra.com.np) <br>
👤 **Lead Developer:** Upahar Mishra <br>
KIST Fair Sci-Tech Exhibition 2082

---

## The Mission

Advanced Driver Assistance Systems (ADAS) have historically been a luxury, locked behind vehicles priced above $60,000, such as Tesla Autopilot and Volvo Pilot Assist. This leaves the vast majority of commercial drivers, including truck, bus, and machinery operators using older vehicles, completely unprotected, even though fatigue related accidents account for nearly 20% of highway fatalities worldwide.

Neuro-Nod Elite exists to **democratize safety**: no environment to set up, no libraries to install, no terminal commands — just a browser tab.

## Features

- **Fatigue Detection via EAR (Eye Aspect Ratio):**  
  Detects microsleeps and prolonged eye closure that traditional motion sensors often fail to identify.

- **3D Head-Drop and Nodding Detection:**  
  Uses real 3D pitch and roll geometry analysis instead of simple 2D pixel tracking to accurately detect head nodding and loss of alertness.

- **Side-Slump and Shoulder-Slump Detection:**  
  Identifies posture-based fatigue signals by monitoring abnormal head and upper-body orientation changes.

- **Yawn Detection via MAR (Mouth Aspect Ratio):**  
  Detects repeated yawning patterns as an additional indicator of driver fatigue.

- **"Sunglasses Paradox" Fail-Safe:**  
  When eye visibility is compromised for more than 2 seconds, the system automatically switches to **Posture-Primary Mode**, ensuring dark lenses never disable the safety mechanism.

- **Face-Lost Failsafe:**  
  Triggers an alert if the driver's face leaves the camera frame or if the camera is intentionally covered.

- **Per-Session Auto-Calibration:**  
  A 3-second calibration window creates a personalized baseline for each driver's neutral posture and eye characteristics.

- **Zero-Latency Local Inference:**  
  All processing happens locally on the device. Video data is never recorded, stored, or transmitted.

- **ACK (Acknowledge) Protocol:**  
  A single tap silences the alarm while confirming that the driver is responsive, reducing unnecessary alerts and preventing alarm fatigue.

## How It Works

### 1. 3D Landmark Geometry
Powered by MediaPipe's 468-point 3D Face Mesh, the system treats the driver's face as a real object moving through 3D space rather than a flat image — using Z-coordinates for genuine depth awareness.

### 2. The Three Mathematical Pillars
- **EAR (Eye Aspect Ratio)** — The system monitors the Euclidean distance between the eyelids. The formula used is:  
This ratio allows the system to detect "Microsleeps" that traditional motion sensors miss. 

$$
EAR = \frac{||p_2 - p_6|| + ||p_3 - p_5||}{2||p_1 - p_4||}
$$

- **IPD (Inter-Pupillary Distance)** — To solve the "Distance-to-Camera" problem, V10.3.1 uses the driver's IPD as a mathematical constant. This ensures that the system's sensitivity remains identical whether the camera is mounted 30cm or 100cm away from the driver. 
- **3D Orientation (Pitch / Roll / Yaw)** — Using the Math.atan2 function, the engine calculates the angular relationship between the nose tip and the chin relative to the camera’s Z-plane. This allows for the detection of "Chin-to-Chest" nodding (Pitch) and "Shoulder-Slumping" (Roll). 

### 3. Signal Processing
Real-world driving is not smooth. Engine vibrations and potholes create "High-Frequency Noise" in the data, leading to false alarms in inferior systems. V10.3.1 implements a sophisticated EMA filter: 

$$
S_t = \alpha \cdot Y_t + (1 - \alpha) \cdot S_{t-1}
$$

Upon initialization, the system enters a 3,000ms Calibration Buffer. During this time, it samples the driver's unique "Neutral State." This accounts for different seating heights, head-rests, and natural eyelid shapes, creating a bespoke mathematical baseline for every individual session. 

### 4. The Watchdog Architecture
Neuro-Nod Elite does not rely on a single "if/else" statement. It employs a Parallel Watchdog Architecture where multiple "Guards" monitor the driver simultaneously. 
#### 4.1 Eye Guard (EAR Watchdog)

**Detection Method:** Eye Aspect Ratio (EAR) Monitoring

- **Threshold:**  
  Eye openness falls below **60% of the driver's baseline openness**.

- **Detection Timer:**  
  **1,500 ms** continuous eye closure.

- **Function:**  
  Detects traditional signs of drowsiness, including:
  - Prolonged eye closure
  - Slow blinking patterns
  - Reduced eye alertness

The Eye Guard acts as the primary layer for identifying fatigue-related visual indicators.

---

#### 4.2 Posture Guard (3D Orientation Watchdog)

**Detection Method:** Head Pose Estimation using 3D orientation tracking

- **Threshold:**
  - **Pitch angle:** > **28°** (head nodding forward)
  - **Roll angle:** > **15°** (head slumping sideways)

- **Detection Timer:**  
  Dynamic range of **800 ms – 1,200 ms**

- **Function:**  
  Detects physical posture collapse even when facial visibility is limited.

This Guard ensures detection reliability in situations where:
- The driver is wearing a hat or head covering.
- The upper facial region is partially obscured.
- Eye-based monitoring becomes unreliable.

---

#### 4.3 Failsafe Guard (Connectivity Watchdog)

**Detection Method:** Facial landmark availability monitoring

- **Threshold:**  
  Loss of more than **90% of facial landmarks**.

- **Detection Timer:**  
  **1,500 ms**

- **Function:**  
  Identifies abnormal camera conditions, including:
  - Driver moving out of the camera frame.
  - Camera being intentionally covered.
  - Sudden tracking failure.

The Failsafe Guard prevents the system from incorrectly assuming driver alertness when monitoring data becomes unavailable.

---


### 5. Adaptive Context Switching (Sunglasses Mode)
The hallmark of V10.3.1 is its ability to handle Visual Obstruction. 
**The Challenge**: 99% of computer vision safety systems become useless the moment a driver puts on dark sunglasses, as the "Eye Guard" loses its data stream. 
**The Neuro-Nod Solution**: We developed a Context-Aware Logic Gate. The system monitors the "Confidence Level" of the eye landmarks. If the system detects that the eyes are obscured for > 2 seconds, it automatically enters Sunglasses Mode. 
In this mode: 
1.	The EAR (Eye Guard) is placed in a "Secondary" state. 
2.	The Posture Guard is elevated to "Primary" status. 
3.	The sensitivity of the Head Pitch detector is increased to compensate for the loss of eye data. 
This ensures that a driver wearing dark lenses on a sunny highway is just as safe as a driver with clear vision. 


## Industrial Applications

- Long-haul logistics : Providing a safety layer for night-shift truck drivers. 
- Construction & warehousing: Monitoring forklift and crane operators where a 2-second lapse in focus can be fatal. 
- Public transport: : A low-cost solution for bus and micro-van drivers on winding mountain roads. 
- Heavy machinery: Integration into industrial control booths. 

## Roadmap (V11 — "Focus Shield")

1.	Yaw-Axis Monitoring: Detecting sideways distraction (looking at phones or passengers). 
2.	IoT Haptic Feedback: Connecting the browser to a vibrating motor in the steering wheel via Bluetooth. 
3.	Night-Vision Optimization: Leveraging IR-capable webcams for monitoring in 0% cabin light. 


## 📁 Project Structure

```
.
├── index.html                     # Landing page
├── logic.pdf                      # Full technical white paper
├── favicon.svg
├── CNAME                          
└── projects/
    └── neuro-nod/
        ├── index.html             
        └── demo.html              
```

###  Tech Stack
<table>
    <tr>
        <td style="font-weight: bold; padding-right: 10px; vertical-align: center;">Frontend:</td>
        <td>
            <img height="40" src="https://skillicons.dev/icons?i=html,css,js"/>
        </td>
    </tr>
    <tr>
        <td style="font-weight: bold; padding-right: 10px; vertical-align: center;">Computer Vision:</td>
        <td>
            <img height="40" src="https://skillicons.dev/icons?i=opencv,tensorflow"/>
        </td>
    </tr>
    <tr>
        <td style="font-weight: bold; padding-right: 10px; vertical-align: center;">Hosting:</td>
        <td>
            <img height="40" src="https://go-skill-icons.vercel.app/api/icons?i=github"/>
        </td>
    </tr>
    <tr>
        <td style="font-weight: bold; padding-right: 10px; vertical-align: center;">IDEs:</td>
        <td>
            <img height="40" src="https://skillicons.dev/icons?i=vscode"/>
        </td>
    </tr>
</table>

> Core CV inference runs on **MediaPipe Face Mesh** (468-point 3D landmarks) + **Camera Utils**, loaded via CDN — no backend, no database, no build step.

---

## Conclusion

Neuro-Nod Elite V10.3.1 is a testament to the power of Edge AI. By combining 3D geometry, signal processing, and adaptive logic, we have transformed a "school project" into a viable, life-saving "safety product." We have proven that safety does not have to be a luxury—it can be a line of code that protects everyone. 


*© 2026 Neuro-Nod Technologies*
