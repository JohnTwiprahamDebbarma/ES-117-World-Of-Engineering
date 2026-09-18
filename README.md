# SwiftSentry — Contactless Fall Detection for Enclosed Spaces

> *There for you, always…*

Detects when someone collapses in a bathroom or other enclosed space and raises an alarm — no camera,
nothing worn on the body. Capstone project for **ES 117: World of Engineering**, IIT Gandhinagar
(Prof. Udit Bhatia).

|  |  |
|---|---|
| **My role** | **Team Captain** — led a 29-member team, one of 10 teams in the course |
| **Term** | May – July 2023 (first year, B.Tech CSE) |
| **Outcome** | **10/10 — highest grade in the course** |
| **Built with** | Arduino Mega 2560, C++, multi-sensor fusion |
| **Constraint** | **₹15,000 hard budget cap** — fabrication in-house on campus laser cutters and 3D printers |

<p align="center">
  <img src="Final%20Diagram.jpeg" alt="System schematic: Arduino Mega with piezo, ultrasonic, laser/photodiode and IR sensors" width="560">
</p>

## The idea

Cameras are unacceptable in a bathroom. Wearables get taken off to shower — exactly when falls happen.
SwiftSentry uses neither.

And the danger isn't the impact, it's the *long lie*: the hours between falling and being found. So the
system doesn't try to detect a fall. It detects **an impact that isn't followed by the person getting
back up.**

**And all of it under ₹15,000.** The course capped the budget hard and expected teams to use IIT
Gandhinagar's own fabrication facilities — laser cutters, 3D printers, the workshop — rather than buy
anything we could make. That pushed the design toward commodity sensing: piezo discs, HC-SR04s and
HW-201 IR modules, all sub-₹100 parts, with the enclosure and mock floor fabricated on campus. It also
made sensor fusion the *cheap* option rather than the expensive one — four crude sensors that
cross-check each other cost a fraction of one sensor good enough to be trusted alone.

## How it works

| Sensor | Question it answers |
|---|---|
| Piezo discs (floor) | Did something hit the floor hard? |
| Ultrasonic (ceiling) | How far is the body from the ceiling — upright or prone? |
| Laser + photodiode | Is the person standing? (a low beam an upright body breaks) |
| IR pair (doorway) | How many people are in the room, and did they enter or leave? |

```text
if people != 1        -> disarm, power down the sensor rail
if piezo impact       -> start a 15 s window
     stands up at any point   -> stand down, no alarm
     still down after 15 s    -> alarm
```

Three decisions that make it work:

- **Arm only for a lone occupant.** Two people means help is already there; zero means nothing to
  protect. Removes a whole class of false alarms for free.
- **Recovery is the signal, not the fall.** Any single "standing" reading during the window cancels the
  alarm — biased toward assuming you're fine, which is the right asymmetry for a device in someone's home.
- **Posture confirmed two ways** (optical *or* acoustic), so a blinded sensor degrades to a slower
  alarm rather than a missed one.

## Known limitations

The project is not tested on a large scale, given the time and budget constraints.

## Leading a 29-member team

A 29-person team is slower than a 5-person one by default — coordination cost grows faster than the
labour available. What made it tractable was splitting the problem into tracks that could genuinely run
in parallel and only had to meet at defined interfaces: requirements, sensor evaluation, fall-kinematics
research, floor-materials study, firmware, circuit fabrication, and communication. My job was holding
those interfaces and keeping every technical decision traceable back to the requirements.

The course graded the engineering and the communication of it: **10/10, the highest in the course.**

## Contents

| File | |
|---|---|
| [`SwiftSentry.ino`](SwiftSentry.ino) | Full sketch — sensor drivers, occupancy state machine, detection logic |
| [`WOE.pdf`](WOE.pdf) | Design deck: requirements, sensor selection, algorithm flowchart, results, poster |
| [`Final Diagram.jpeg`](Final%20Diagram.jpeg) | Annotated schematic (from [`Diagram-1.jpeg`](Diagram-1.jpeg)) |

MIT licensed · Built by **Troubleshooters~30**, captained by **John Twipraham Debbarma**.
