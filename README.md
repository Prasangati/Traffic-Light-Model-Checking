# Traffic-Light-Model-Checking

# 🚦 Traffic Light Model Checking  
A **NuSMV-based model checking project** verifying the correctness of a **multi-lane traffic light system** using **temporal logic (CTL & LTL)**.

## **🛣️ Problem Description**
This project models a **W-E one-way road with three lanes merging into one** and a **two-way pedestrian crossing** with **walk/don't-walk (WDW) signals**. The system follows these rules:

- 🚗 **Traffic lights cycle**: Each lane stays **green for 2 cycles**, switching **S → M → N**.
- 🚶 **Pedestrian crossing**: A button press triggers **"walk" for 3 cycles** before normal operation resumes.
- 🔄 **Concurrency considerations**: Pedestrian signals may extend if buttons are pressed at the last second.
- ❗ **Verification Goals**: Ensure traffic **safety, fairness**, and **non-starvation of lanes**.

## **🛠️ Features**
✅ **NuSMV Model**: Encodes traffic signals, pedestrian buttons, and light transitions.  
✅ **Formal Verification**: Uses **CTL & LTL** to verify system safety and fairness.  
✅ **Counterexample Analysis**: Identifies cases where the system fails.  
✅ **Fairness Testing**: Ensures no lane is indefinitely blocked.  

## **📁 File Structure**
- [`traffic_light.smv`](traffic_light.smv) → **NuSMV model** of the traffic system.  
- [`properties.md`](properties.md) → Explanation of **CTL & LTL properties** being checked.  
- [`counterexamples.md`](counterexamples.md) → **Analysis of counterexamples** from NuSMV.  
- [`results.txt`](results.txt) → **NuSMV output logs** showing verification results.  
- [`docs/`](docs/) → Additional diagrams and documentation (if needed).  

## **⚙️ How to Run the Model**
### **1️⃣ Install NuSMV**
Download and install **NuSMV**:  
📌 [NuSMV Official Download](https://nusmv.fbk.eu/NuSMV/download/getting-v2.html)

### **2️⃣ Run the Model**
```bash
NuSMV traffic_light.smv
