# From Bode Plot to Breadboard: Bringing Control Theory into Practice

This project started as a purely **theoretical exercise** and evolved into a **fully working analog circuit**.  
The goal was to translate a Bode magnitude approximation into a real implementation while understanding the gaps between **theory, simulation, and hardware**.

---

## 1. Starting Point: Theoretical Problem

The starting point was a given **Bode magnitude approximation**.

From the plot, I extracted:
- Corner (break) frequencies
- Magnitude slopes (+20 dB/dec and −20 dB/dec)
- Midband gain

Using this information, I derived the corresponding **transfer function** analytically.  
At the center frequency, the expected gain was approximately **20 dB**, corresponding to a gain factor of **10**.

---

## 2. From Transfer Function to Circuit

Once the transfer function was defined, I mapped it to an **active filter topology using an op-amp**.

To make the design practical:
- A **conversion table** was used to rescale the component values
- This allowed the use of **standard resistor and capacitor values**
- All components were available in my university FabLab

At this stage, the design was still based on **ideal assumptions**.

---

## 3. Simulation with an Ideal Op-Amp (LTspice)

The first verification step was simulation in **LTspice using an ideal op-amp**.

Results:
- AC analysis showed a gain **15 dB at ~156 Hz** (little bit lower than 20dB somehow)
- Clean sinusoidal behavior
- No distortion or bandwidth limitations

This confirmed that the **mathematical design matched the intended frequency response**.

---

## 4. Transition to a Real Op-Amp (LM324)

In hardware, op-amps are not ideal.  
For the physical implementation, I used the **LM324**, which introduced several practical constraints:

- Single-supply operation
- Limited input and output voltage swing
- Need for proper DC biasing

To ensure correct operation, the **non-inverting (+) input** was biased at mid-supply:

\[
V_{\text{bias}} = \frac{3.3\ \text{V}}{2}
\]

This biasing allowed the AC signal to swing symmetrically around a valid operating point. (here we need to worry about the max amplitude for input signal)

---

## 5. Input Signal Amplitude Limitations

With the real op-amp model, another practical limitation became evident:

- The input sinusoidal signal had to be limited to **~200 mV (peak)** (as said above)
- Larger amplitudes resulted in **output clipping and distortion**

This behavior is due to the **finite output swing and headroom** of the LM324 and is not visible when using ideal op-amp models.

---

## 6. Hardware Implementation and Measurements

The circuit was then implemented on a **breadboard** and tested using laboratory equipment.

Test conditions:
- Function generator amplitude: ≈ 200 mV
- Input frequency: **156 Hz** (chosen from AC analysis where gain gets highest)

Oscilloscope measurements:
- Input signal ≈ 200 mV
- Output signal > 600 mV

Measured gain:
\[
20 \log_{10}\left(\frac{600\ \text{mV}}{200\ \text{mV}}\right) \approx 10\ \text{dB}
\]

This is lower than expected. However, the reason came below.

---

## 7. A Key Practical Insight: The Hidden 50 Ω Source Resistance

Initially, my LTspice simulation predicted a higher gain (≈15 dB at 15.6 Hz) than what I measured in hardware.

The discrepancy was explained by an often-overlooked detail:

> **Most function generators have an internal 50 Ω output impedance.**

In the original simulation, this resistance was not included.  
After adding a **50 Ω series resistor** before the input resistor in LTspice, the simulation results aligned closely with the experimental measurements. (check the schematics in LTSpice)

This confirmed that **test equipment is part of the circuit** and must be modeled accordingly.

---

## 8. Key Takeaways

- Bode plots can be systematically translated into real circuits
- Ideal simulations are necessary but not sufficient
- Real op-amps introduce biasing, swing, and amplitude constraints
- Measurement equipment can significantly affect results
- The full engineering workflow is:
  
  **Theory → Simulation → Hardware → Measurement → Model Refinement**

Bridging these steps is where theoretical understanding turns into practical engineering skill.
