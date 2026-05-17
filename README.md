
# NPC Lab for the Event-Driven Vision School 2026 

### Neuro-inspired Perception and Cognition (NPC) Lab — Czech Technical University in Prague

This tutorial series is part of the teaching activities of the [**Neuroinspired Perception and Cognition (NPC) Lab**](https://giuliadangelo.github.io/html/npclab.html) at the 
**Department of Cybernetics, Faculty of Electrical Engineering, Czech Technical University 
in Prague**, led by [Assistant Professor Giulia D'Angelo](https://giuliadangelo.github.io/). 
It introduces students to the computational principles of spiking neurons, 
the fundamental building blocks of neuromorphic computing and brain-inspired 
artificial intelligence.

These tutorials are designed and developed by the NPC Lab team: 
- Giulia D'Angelo (giulia.dangelo@fel.cvut.cz)  (Principal Investigator)
- Sarka Liskova (sarka.liskova@fel.cvut.cz) (PhD student)
- Paolo Ritirato (paolo.ritirato@fel.cvut.cz) (PhD student)


## Introduction

The brain processes information through electrical impulses 
called **spikes** or **action potentials**. 
Unlike conventional artificial neurons that 
pass continuous-valued activations, biological neurons 
communicate through discrete, all-or-nothing events, and the 
timing of those events carries meaning.

Understanding how a single neuron integrates input, 
reaches threshold, fires, and resets is the foundation 
of everything in neuromorphic computing: from spiking neural 
networks to event-based vision to brain-inspired robotics.



This tutorial series starts from the simplest biologically 
plausible neuron model and progressively introduces biological constraints, 
such as the **refractory period**, that shape real neural dynamics, 
before scaling up to spiking neural networks (SNNs) and a full biologically 
inspired **Object Motion Sensitivity (OMS)** SNN model.

---

## Tutorial Overview

```
Tutorial 1A    LIF neuron — membrane dynamics and spike generation
Tutorial 2    LIF neuron with refractory period
Tutorial 3     Spiking Neural Networks (Brian2)
Tutorial 4     Object Motion Sensitivity (OMS) model
```


---

## Getting Started

```bash
1. Clone the repository
git clone https://github.com/Neuro-inspired-Perception-and-Cognition/EVSchool2026.git
cd EVSchool2026

2. Create a virtual environment
python -m venv .venv

Activate it:
macOS/Linux:   source .venv/bin/activate
Windows:       .venv\Scripts\activate

3. Install dependencies
pip install -r requirements.txt

4. Run Tutorial 1A
python Tutorial1A-LIFNeuron.py
```

---

## Tutorial 1A — The Leaky Integrate-and-Fire Neuron

**Script:** [`Tutorial1-LIFNeuron.py`](Tutorial1-LIFneuron.py)


This tutorial simulates a **Leaky Integrate-and-Fire (LIF)** neuron, the most
widely used model in computational neuroscience. 
The membrane potential is computed numerically and visualised 
in response to a series of external input current pulses.

> 💡 **Key concept:** The LIF model captures the essential 
> dynamics of biological neurons: integrate incoming charge, 
> leak over time, fire when a threshold is reached, then reset. 
> It is computationally simple yet biologically meaningful.

The governing equation is:

```
dV/dt = (-(V - V_rest) + R·I(t)) / τ_m

If V ≥ V_threshold  →  spike emitted, V reset to V_rest
```

### Parameters

| Parameter | Symbol | Typical Value | Description |
|---|---|---|---|
| Membrane capacitance | `Cm` | 100 pF | Capacity to store charge |
| Leak conductance | `gL` | 10 nS | Rate of passive voltage decay |
| Resting potential | `V_rest` | −70 mV | Equilibrium in absence of input |
| Threshold | `V_th` | −50 mV | Voltage at which a spike is emitted |
| Reset potential | `V_reset` | −70 mV | Voltage after spike |
| Membrane time constant | `τ_m` | `Cm / gL` | Timescale of integration and leak |

### Questions

1. Find the minimal constant current $I_{min}$ which causes the neuron to fire at least once. *(See Task 2 in the exercise above.)*
2. How does changing the amplitude and duration of the input current pulses affect the firing behaviour? Try halving and doubling both values.
3. What happens when you modify `Cm`, `gL`, or `V_th`? For each parameter, predict the effect before running the simulation, then verify. *(See Task 1 in the exercise above.)*

### Exercises

**Task 1 — Explore LIF parameters**

Modify the LIF parameters `Cm`, `gL`, or `VT` and observe how the membrane potential and spiking behaviour change in the animation. What happens when you increase or decrease each value independently?

**Task 2 — Find the minimum firing current**

Now consider a constant current $I_{ext}$ instead of the pulsed input. Find the minimal constant current $I_{min}$ which causes the neuron to fire at least once.

The LIF equation under constant input is:

$$\tau_m \frac{dV(t)}{dt} = (V_L - V(t)) + R_m \cdot I_{ext}$$

> 💡 **Hint:** At steady state $\frac{dV}{dt} = 0$. The spiking condition is $V = V_T$. Solve for $I_{ext}$.

```python
# TODO: solve for I_min here
I_min = None
print(f"Minimum input current required to reach threshold: {I_min:.2f} uA")
```
---

## Tutorial 2 — The LIF Neuron with Refractory Period

**Script:** [`Tutorial2-NeuronRefractory`](Tutorial2-NeuronRefractory.py)

This tutorial extends the LIF model by introducing 
the **refractory period**, the brief interval after a 
spike during which a neuron cannot fire again, regardless of input. 
This is a fundamental biological constraint that shapes neural 
coding and network dynamics.

> 💡 **Key concept:** After a spike, 
> biological neurons enter a refractory
> state due to ion channel dynamics. 
> During the **absolute refractory period**, 
> no spike is possible. This limits the maximum 
> firing rate and plays a crucial role in temporal coding.

### Exercises

**Task 1 — Observe the refractory period**

Run the script and compare the membrane potential and spike trains of the neuron with and without refractory period. 
What do you notice about the firing rate?

**Task 2 — Explore the firing rate vs. input current**

Adapt the code to feed the neuron a constant external 
current $I_{ext}$. Use 10 equidistant values 
from $[I_{min}, 18]\ \mu A$, where $I_{min}$ is your result from
Tutorial 1. 
For each current value, compute the firing rate 
(spikes/sec) for both scenarios and plot them against $I_{ext}$.

```python
# TODO
firing_rate = None
firing_rate_r = None
print(f"Difference in firing rates: {firing_rate[-1] - firing_rate_r[-1]} spikes/sec at I_ext = 18 uA.")
```

### Questions

1. What is the difference in spike rate with and without refractory period for $I_{ext} = 18\ \mu A$?
2. How does increasing the refractory period duration $\tau_{ref}$ affect the maximum achievable firing rate?
3. Can you find a current value where the refractory period prevents additional spikes that would otherwise occur?


## Tutorial 3A — Spiking Neural Networks (Brian2)

**Script:** [`Tutorial3A-SNN.py`](Tutorial3A-SNN.py)

This tutorial introduces **network-level** spiking dynamics using the **Brian2** simulator. A population of 100 independent LIF neurons is simulated, each with a different baseline membrane potential, and their spiking activity and firing rates are visualised.

> 💡 **Key concept:** Some biological neurons fire spontaneously without external input — for example, brainstem neurons that drive breathing. This intrinsic excitability is modelled via the baseline membrane potential `v0`: the higher it is, the more often the neuron fires on its own.

### Exercise

**Task 1 — Observe spontaneous activity**

Run the simulation and observe at which times each neuron fires and how the baseline potential controls the firing rate.

Then adjust the `tau` parameter and observe how the activity changes for:

```
tau = 2.0 ms
tau = 10.0 ms
tau = 50.0 ms
```

### Questions

1. Which neurons fire most frequently and why?
2. How does increasing `tau` affect the firing rate across the population?
3. What is the relationship between `v0` and firing rate — is it linear?


## Tutorial 3B — Spiking Neural Networks (Brian2)

**Script:** [`Tutorial3B-SNN.py`](Tutorial3B-SNN.py)

This tutorial introduces **network-level** 
spiking dynamics using the **Brian2** simulator. 
A chain of 10 LIF neurons is connected via synapses, 
and signal propagation is visualised in real time. 
Only one neuron fires spontaneously, the rest respond only through synaptic input.

> 💡 **Key concept:** Real neural computation emerges at 
> the *network* level. This tutorial demonstrates how a 
> spike initiated by a single neuron propagates through a
> chain, and how synaptic strength, connection direction, 
> and refractory period all shape that propagation.

### Exercise

**Task 1 — Observe signal propagation**

Run the script and observe how the spike initiated by the middle 
neuron spreads in both directions along the chain. 
Play with the `tau` parameter and observe how it influences the system.

Then:
- Set `tau = 10*ms`
- Disable right-to-left connections by removing `S.connect(condition='j == i-1 and i > 0')`
- Move the non-zero baseline potential to the **first neuron** instead of the middle one

How far does the signal travel? Which is the most distant neuron to spike?

**Task 2 — Explore connection strength**

With the unidirectional chain from Task 1, 
explore weight values in the range $[0.9, 1.1]$. 
How does spiking behaviour change?

**Task 3 — Explore the refractory period**

Set weights back to `w = 1.0` and decrease the refractory period from `5*ms` down to `1*ms`. How far does the signal spread for `1*ms`?

### Questions

1. How far does the signal travel when connections are unidirectional and the first neuron drives the chain?
2. What happens to signal propagation when `w < 1.0`? And when `w > 1.0`?
3. How far does the signal spread with a refractory period of `1*ms`?


## Tutorial 4 — Object Motion Sensitivity (OMS)

**Script:** [`Tutorial4-OMS.py`](Tutorial4-OMS.py)

This tutorial implements an **Object Motion Sensitivity (OMS)** network, 
a biologically inspired mechanism for motion segmentation directly 
derived from the research of the NPC Lab. 
The system analyses event-based frames using 
centre-surround spatial kernels to detect local motion differences, 
producing a segmentation map comparable with ground-truth masks.

> 💡 **Key concept:** OMS cells are modelled on retinal 
> amacrine cells that respond selectively to 
> objects moving differently from their static background.
> 
> This work is directly connected to [D'Angelo et al., 2025](https://doi.org/10.1088/2634-4386/addc90).

📥 **Download data:** [Dropbox link](https://www.dropbox.com/scl/fo/g5j17yh6elrc61s66aiba/AO2lSvWa5oLlZYhc0V2CNkw?rlkey=w0hgpsbd2mjvbfp4vrtdm5bhe&st=hi09k9to&dl=0)

Place the data in `data/evimo/` before running the script.

### Exercise

**Task 1 — Observe OMS segmentation**

Run the script and observe the three panels: the input event frame, the ground-truth segmentation mask, and the OMS output. Press `ESC` to stop the visualisation at any time.

**Task 2 — Compute the OMS motion score**

For a randomly selected frame, compute the percentage of pixels classified as motion by the OMS network:

```python
# TODO: compute the motion ratio (%) for the selected frame index
motion_ratio = None
print(f"Frame index: {frame_idx}")
print(f"Motion ratio (%): {motion_ratio:.2f}")
```

**Task 3 — Explore OMS parameters**

Vary the parameters in `OMS_PARAMS` (threshold, kernel sizes, sigma values) and observe how segmentation quality changes. Which parameters have the most impact?

### Questions

1. How does the difference between centre and surround responses contribute to motion segmentation?
2. How does the choice of `threshold` affect the OMS output — what happens at very low or very high values?
3. Which `OMS_PARAMS` have the most impact on segmentation quality and why?

---

## Bonus — Live Demo: OMS on Neuromorphic Hardware

For a live demonstration of the OMS pipeline deployed on the
**SynSense Speck2f** neuromorphic chip, see the companion repository:

[github.com/GiuliaDAngelo/Speckegomotion](https://github.com/GiuliaDAngelo/Speckegomotion)

This demo is directly connected to [D'Angelo et al., 2025](https://doi.org/10.1088/2634-4386/addc90) and shows the full OMS, visual attention and control pipeline running in real time on neuromorphic hardware with event-based eye movements under two conditions:

- **Microsaccades** — small continuous jitter movements around a fixed position, producing a steady stream of events even in a static scene
- **Saccades** — large ballistic jumps to random positions, alternating between dense event bursts and complete silence

The two conditions reveal how eye movement strategy shapes the output of the OMS network on-chip.

---

## More Tutorials

For more tutorials on event-driven sensing and neuromorphic computing, visit:

[github.com/GiuliaDAngelo/CTU-EDNeuromorphic](https://github.com/GiuliaDAngelo/CTU-EDNeuromorphic)

*NPC Lab — Czech Technical University in Prague | Faculty of Electrical Engineering | Department of Cybernetics*
*Contact: [giulia.dangelo@fel.cvut.cz](mailto:giulia.dangelo@fel.cvut.cz)*
