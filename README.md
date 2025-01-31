# 🚦 Traffic Light Controller Model Checking  

## **Name**  
Prasanga Tiwari  

## **Goal of the Project**  
The goal of this project is to apply **model checking** and **temporal logic** to solve a **real-world problem** involving a **traffic light controller**. The project models and verifies a **multi-lane road intersection with pedestrian crossings** to ensure it follows safety and fairness constraints.  

---

## **Problem Description**  
This model represents a **three-lane, one-way road** with **two pedestrian walk/don't-walk (WDW) signals**.  

### **System Components**  
- 🚗 **Traffic Lights:** Three lane-specific red-green signals.  
- 🚶 **Pedestrian Signals:** Two WDW lights controlled by button presses.  
- 🔘 **Button Inputs:** Two pedestrian buttons trigger the walk signal.  
- 🔄 **Traffic Light Rules:**
  - A **lane stays green for 2 cycles** before switching to red.
  - Lanes are served in order: **South → Middle → North**.
  - If a **pedestrian button is pressed**, both WDW lights turn **walk** for **3 cycles** before resuming normal traffic flow.

### **Concurrency Considerations**  
- Both **WDW lights are synchronized**.  
- If a button is pressed **during a walk signal**, the walk phase extends by **2 cycles**.  
- A button press **immediately after a walk phase** will schedule a **new walk phase**, but only after at least one lane has received a green light.

---

## **Steps in Model Checking**
This project follows the standard **3 steps in model checking**:

1. **Modeling** the system as **SMV state machines**.
2. **Writing correctness properties** in **LTL and CTL**.
3. **Running NuSMV** to verify the properties.

Since this is a **simple system**, model checking is **not strictly necessary**, but it serves as an **exercise in formal verification**.

---

## **State Machine Model**
The system is modeled as a **finite-state machine** in **NuSMV**, with the following key variables:

| **Variable**  | **Description** |
|--------------|----------------|
| `lane1`, `lane2`, `lane3` | Boolean values representing whether a lane’s light is green |
| `cycle` | Tracks the traffic light cycle (0-2) |
| `current` | Indicates the lane currently receiving a green signal |
| `northwdw`, `southwdw` | Boolean values representing pedestrian signals |
| `button1`, `button2` | Boolean values representing pedestrian button presses |
| `nextwalk` | Boolean flag indicating whether the next phase should activate a walk signal |
| `counter` | Tracks the remaining cycles for the walk signal |

Each **lane, walk signal, and button input** has its own **state transition rules** encoded in NuSMV.

---

## **Property Specifications**
Each correctness property is written in **LTL or CTL**.

| **#** | **Property Description** | **Specification** | **Status** |
|------|-----------------|-------------------|------------|
| **1** | No two adjacent lanes are green simultaneously | `AG(!lane1 \| !lane2)` | ✅ Passed |
| **2** | Cars do not hit pedestrians | `G(!lane3 \| !northwdw)` | ✅ Passed |
| **3** | Center lane will eventually be green | `G F(lane2)` | ❌ Failed (Counterexample found) |
| **4** | Successive greens for the south lane are separated by 8 cycles | *(See LTL in `traffic_light.smv`)* | ✅ Passed |
| **5** | Each lane gets a green before the next pedestrian walk signal | *(See LTL in `traffic_light.smv`)* | ❌ Failed (Fairness issue) |

### **Key Issues Identified**
- **Starvation of lanes** can occur if **pedestrian buttons are pressed at the last second**, preventing traffic lights from switching.
- **Fairness violations** occur when some lanes **never receive a green light**.

For more details, see [`counterexamples.md`](counterexamples.md).

---

## **Running NuSMV**
### **1️⃣ Install NuSMV**
You can install **NuSMV 2.6.0 (non-ZCHAFF version)** from:  
📌 [NuSMV Official Download](https://nusmv.fbk.eu/downloads.html)


### **2️⃣ Running NuSMV on Your Local Machine**
If you've installed NuSMV locally, navigate to the folder where your `.smv` file is located and run. For me it was:
```bash
/Users/prasangatiwari/Downloads/NuSMV-2.6.0-Darwin/bin/NuSMV traffic_light.smv.txt

