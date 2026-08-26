# 🚗 Real-Time Vehicle Counting with YOLO26s

A real-time vehicle counting system built with **YOLO26s**, featuring two independent counting approaches:

1. **Ultralytics ObjectCounter** — using the built-in counting solution provided by Ultralytics.
2. **Custom Vehicle Counting** — using YOLO26s tracking IDs and a custom line-crossing algorithm.

The project demonstrates how different counting strategies can be implemented using YOLO-based object detection and tracking for real-time traffic analysis.

---

## ✨ Features

* 🚗 Real-time object detection using **YOLO26s**
* 🎯 Object tracking with unique tracking IDs
* 📊 Real-time vehicle/object counting
* 📍 Configurable counting region
* ↔️ Left/right movement detection
* 🧠 Custom line-crossing counting algorithm
* 🔄 Two independent counting approaches
* 🎥 Video processing with OpenCV
* 📓 Jupyter Notebook implementations

---

## 🧠 Methods

### 1️⃣ Ultralytics ObjectCounter

The first approach uses the built-in `ObjectCounter` solution provided by Ultralytics.

```text
Input Video
     ↓
YOLO26s
     ↓
Detection & Tracking
     ↓
Ultralytics ObjectCounter
     ↓
Counting Region
     ↓
Object Count
```

A rectangular counting region is defined using four points:

```python
region_points = [
    (20, 400),
    (1080, 400),
    (1080, 360),
    (20, 360)
]
```

The counter is initialized with YOLO26s:

```python
counter = solutions.ObjectCounter(
    show=True,
    region=region_points,
    model="../yolo26s.pt",
)
```

No class filtering is applied in the current implementation.

### Output

<img width="384" height="216" alt="object_counter_ultralytics_output" src="https://github.com/user-attachments/assets/c7b14d2c-bcb5-44c4-b9f9-b008ae4751b7" />

---

### 2️⃣ Custom Vehicle Counting

The second approach implements the counting logic manually using YOLO26s tracking results.

Instead of using the built-in `ObjectCounter`, this method directly accesses tracking IDs and bounding-box center coordinates.

```text
Input Video
     ↓
YOLO26s
     ↓
Object Tracking
     ↓
Tracking ID
     ↓
Bounding Box Center
     ↓
Line-Crossing Detection
     ↓
Left / Right Direction
     ↓
Object Count
```

### Line-Crossing Detection

The vertical center coordinate `cy` is used to determine whether an object crosses the horizontal counting line.

```python
crossed_line = (
    (previous_cy < center[1] and cy >= center[1])
    or
    (previous_cy > center[1] and cy <= center[1])
)
```

### Direction Detection

After detecting a line crossing, the horizontal center coordinate `cx` determines the object's position relative to the reference center:

```python
if cx < center[0]:
    car_count_left += 1

elif cx > center[0]:
    car_count_right += 1
```

Reference center:

```python
center = [590, 400]
```

Therefore:

* `cy` → detects **line crossing**
* `cx` → determines **left/right position**

### Duplicate Prevention

Each tracking ID is stored after being counted:

```python
if crossed_line and object_id not in id_list:
```

This prevents the same tracked object from being counted multiple times.

### Output
<img width="512" height="288" alt="custom_vehicle_counter_output" src="https://github.com/user-attachments/assets/b7791830-7649-4533-ba73-ec54b7dfb743" />
---

## ⚖️ Method Comparison

| Feature              | Ultralytics ObjectCounter | Custom Method     |
| -------------------- | ------------------------- | ----------------- |
| Model                | YOLO26s                   | YOLO26s           |
| Tracking             | Built-in Ultralytics      | YOLO Tracking     |
| Counting             | Built-in ObjectCounter    | Custom algorithm  |
| Counting Region      | Rectangular region        | Horizontal line   |
| Direction Detection  | Built-in                  | Custom `cx` logic |
| Tracking IDs         | Managed internally        | Directly accessed |
| Duplicate Prevention | Built-in                  | `id_list`         |
| Customization        | Limited                   | High              |

### 🔍 Main Difference

The main difference between the two approaches is the **counting logic**.

The Ultralytics approach delegates the counting process to the built-in `ObjectCounter`.

The custom approach directly uses tracking information and implements the counting logic manually. It stores the previous position of each tracking ID, detects line crossings using `cy`, and determines the left/right position using `cx`.

This provides greater control over the counting rules and allows the algorithm to be adapted to project-specific traffic scenarios.

---

## 📂 Project Structure

```text
Real-Time-Vehicle-Counting-with-YOLO26s/
│
├── README.md
├── requirements.txt
├── .gitignore
├── yolo26s.pt
│
├── YOLO-Tracking/
│   └── object_counter_ultralytics.ipynb
│
└── Custom-Method/
│   └── custom_vehicle_counter.ipynb
│
└── result/
    └── object_counter_ultralytics_output
    └── custom_vehicle_counter_output
```

---

## 🛠️ Installation

### 1. Clone the Repository

```bash
git clone https://github.com/Ramin0036/Real-Time-Vehicle-Counting-with-YOLO26s.git
cd Real-Time-Vehicle-Counting-with-YOLO26s
```

### 2. Create a Virtual Environment

#### Windows

```bash
python -m venv venv
venv\Scripts\activate
```

#### Linux / WSL

```bash
python -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

---

## 📦 Requirements

```text
ultralytics==8.4.114
opencv-python
numpy
matplotlib
```

For GPU acceleration, install a PyTorch version compatible with your system and CUDA environment.

---

## ▶️ Usage

Open one of the following notebooks:

### Ultralytics ObjectCounter

```text
YOLO-Tracking/object_counter_ultralytics.ipynb
```

### Custom Method

```text
Custom-Method/custom_vehicle_counter.ipynb
```

The YOLO26s model is loaded from the repository root:

```python
../yolo26s.pt
```

---

## 📍 Counting Region

The current counting region is defined as:

```python
region_points = [
    (20, 400),
    (1080, 400),
    (1080, 360),
    (20, 360)
]
```

The custom method uses the horizontal reference line at:

```python
center = [590, 400]
```

These coordinates can be adjusted according to the camera position and video resolution.

---

## 🎥 Output

### Ultralytics ObjectCounter

```text
object_counting_output.avi
```

### Custom Method

```text
output.mp4
```

Generated video files are excluded from version control through `.gitignore`.

---

## 🚀 Future Improvements

* 🚗 Class-specific counting for cars, trucks, buses, and other vehicles
* 📊 Separate statistics for each vehicle class
* 📈 Traffic-flow visualization
* ⏱️ Vehicle counting over time
* 🎯 Improved line-crossing logic
* ⚡ FPS and performance comparison
* 🎥 Real-time camera and RTSP stream support
* 📊 Accuracy comparison between the two approaches

---

## 🧰 Technologies

* **Python**
* **YOLO26s**
* **Ultralytics**
* **OpenCV**
* **NumPy**
* **Matplotlib**
* **Jupyter Notebook**

---

## 📌 Project Status

🚧 **In Development**

The current version focuses on comparing the built-in Ultralytics ObjectCounter with a custom YOLO26s tracking-based counting algorithm.

---

## 📄 License

This project is intended for educational and research purposes.
