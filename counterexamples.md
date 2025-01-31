# 🚦 Counterexamples for Traffic Light Controller

## **Counterexample 1: "Center lane will eventually be green" (Fails)**
### **Property Specification**
```
LTLSPEC G F(lane2)
-- specification  G ( F lane2)  is false
-- as demonstrated by the following execution sequence
Trace Description: LTL Counterexample 
Trace Type: Counterexample 
  -> State: 1.1 <-
    lane1 = FALSE
    lane2 = FALSE
    lane3 = TRUE
    cycle = 1
    current = 3
    northwdw = FALSE
    southwdw = FALSE
    button1 = FALSE
    button2 = FALSE
    nextwalk = FALSE
    counter = 0
  -> State: 1.2 <-
    cycle = 2
    button1 = TRUE
  -> State: 1.3 <-
    lane3 = FALSE
    cycle = 0
    button1 = FALSE
    nextwalk = TRUE
  -> State: 1.4 <-
    northwdw = TRUE
    southwdw = TRUE
    nextwalk = FALSE
    counter = 1
  -> State: 1.5 <-
    counter = 2
  -- Loop starts here
  -> State: 1.6 <-
    button1 = TRUE
    counter = 3
  -> State: 1.7 <-
    button1 = FALSE
    nextwalk = TRUE
    counter = 2
  -> State: 1.8 <-
    button1 = TRUE
    nextwalk = FALSE
    counter = 3
```
Expected Behavior:
The center lane (lane2) should eventually receive a green light infinitely often.
This ensures no traffic lane is permanently blocked.

What the Counterexample Shows:
Initially, lane3 is green.
A pedestrian presses button1, triggering the walk signal.
WDW lights turn on (northwdw = TRUE, southwdw = TRUE).
Pedestrian requests keep coming right before the WDW lights turn off, extending the walk phase indefinitely.
Because the walk phase never ends, lane transitions never resume.
Lane2 never turns green, contradicting the property.

Why This Happens:
There is no limit on how often pedestrians can press the button.
Walk signals override lane transitions, creating a starvation condition. 

Real-World Implication:
🚨 Certain lanes may never get a green light, leading to unfair traffic flow and congestion.
✅ Possible Fix: Implement a guaranteed cycle rule to ensure each lane gets a green before another pedestrian request.

## **Counterexample 2: "Each lane gets a green before the next pedestrian walk signal" (Fails)**
### **Property Specification**
```
LTLSPEC G (((northwdw | southwdw) & X(!northwdw & !southwdw)) -> 
  (!northwdw & !southwdw) U F (lane1 & F (lane2 & F (lane3))))
-- specification  G (((northwdw | southwdw) &  X (!northwdw & !southwdw)) -> 
  ((!northwdw & !southwdw) U ( F (lane1 &  F (lane2 &  F lane3)))))  is false
-- as demonstrated by the following execution sequence
Trace Description: LTL Counterexample 
Trace Type: Counterexample 
  -> State: 2.1 <-
    lane1 = FALSE
    lane2 = FALSE
    lane3 = TRUE
    cycle = 1
    current = 3
    northwdw = FALSE
    southwdw = FALSE
    button1 = FALSE
    button2 = FALSE
    nextwalk = FALSE
    counter = 0
  -> State: 2.2 <-
    cycle = 2
  -> State: 2.3 <-
    lane3 = FALSE
    cycle = 0
  -> State: 2.4 <-
    lane2 = TRUE
    cycle = 1
    current = 2
  -> State: 2.5 <-
    cycle = 2
  -> State: 2.6 <-
    lane2 = FALSE
    cycle = 0
  -> State: 2.7 <-
    lane1 = TRUE
    cycle = 1
    current = 1
  -> State: 2.8 <-
    cycle = 2
    button1 = TRUE
  -> State: 2.9 <-
    lane1 = FALSE
    cycle = 0
    button1 = FALSE
    nextwalk = TRUE
  -> State: 2.10 <-
    northwdw = TRUE
    southwdw = TRUE
    nextwalk = FALSE
    counter = 1
  -> State: 2.11 <-
    counter = 2
  -> State: 2.12 <-
    counter = 3
  -> State: 2.13 <-
    northwdw = FALSE
    southwdw = FALSE
    counter = 0
  -> State: 2.14 <-
    lane3 = TRUE
    cycle = 1
    current = 3
  -> State: 2.15 <-
    cycle = 2
  -> State: 2.16 <-
    lane3 = FALSE
    cycle = 0
  -> State: 2.17 <-
    lane2 = TRUE
    cycle = 1
    current = 2
  -> State: 2.18 <-
    cycle = 2
    button1 = TRUE
  -> State: 2.19 <-
    lane2 = FALSE
    cycle = 0
    button1 = FALSE
    nextwalk = TRUE
  -> State: 2.20 <-
    northwdw = TRUE
    southwdw = TRUE
    nextwalk = FALSE
    counter = 1
  -> State: 2.21 <-
    counter = 2
  -- Loop starts here
  -> State: 2.22 <-
    button1 = TRUE
    counter = 3
  -> State: 2.23 <-
    button1 = FALSE
    nextwalk = TRUE
    counter = 2
  -> State: 2.24 <-
    button1 = TRUE
    nextwalk = FALSE
    counter = 3
```
Expected Behavior:
Before a new pedestrian walk signal, each lane (lane1, lane2, lane3) should receive a green light at least once.
This ensures traffic moves fairly.

What the Counterexample Shows:
Traffic starts cycling normally (lane3 → lane2 → lane1).
A pedestrian presses button1 while lane1 is green.
WDW lights turn on (northwdw = TRUE, southwdw = TRUE).
Lane1 and Lane2 get a green light, but Lane3 does not before another pedestrian request.
A loop forms where pedestrian requests keep interrupting lane cycles.

Why This Happens:
Pedestrian requests are processed immediately, without waiting for all lanes to complete their cycle.
Some lanes may be skipped indefinitely.

Real-World Implication:
🚨 Certain lanes may never get a green light, leading to unfair traffic flow and congestion.
✅ Possible Fix: Introduce a cooldown period before a pedestrian request can be processed again.
