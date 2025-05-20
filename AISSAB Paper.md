# **RadSee: See Your Handwriting Through WallsUsing FMCW Radar**


## Introduction

> **CONCEPT**
> Designing and implementing a radio device capable of detecting a person handwriting throught a wall.
> The device is also resilient to interference.

> **HARDWARE**
> RadSee is a 6 GHz frequency modulated continuous wave (FMCW) radar system. 
> Components:
> - 6GHz FMCW radar;
> - custom designed high-gain patch antennas;

> **RESULTS**
> RadSee achieves 75% letter recognition accuracy when victims write 62 random letters, and 87% word recognition accuracy when they write articles.

![[Pasted image 20250515142231.png]]

## The challenge

> **HAR**
> Stands for Human Activity Recognition.

Previous har attempts:
- cameras; 
- ultrasound; 
- Wi-Fi;
- RFID;
- millimeter-wave (mmWave) radar.

The project challenge:
1. Detection **trougth a wall** significantly **limits** the viable **sensing techniques** for this task (no cam (light condition), no ultrasound (radar problem throught wall), Radio frequency the same cause they use high frequency);
   
2. **Millimiter level precision** is need to track hand moovements: high frequency mmWave are capable of capting small moovements, but can't pass trougt walls.
   
3. **Interference resiliance**: handwriting detection could soffer from other mooving objects in the same room.

How RadSee address this challenges:
1. Solved by means of FMCW, high-gain patch antenna and baseband analog filter. The optimized antenna has 36 dBi gain for wall penetration;
2. Using the phase information of demodulated FMCW signals:
   - Range resolution:$$R = {c\over 2B};$$
   where $c$ is the speed of light and $B$ is the bandwith of the antennas.
   To achieve range resolution of $1mm$ it requires $$B = {3\times 10^8\over 2\times 10^{-3}} = 150GHz;$$
   which is impossible in practice.
   However, the phase of demodulated FMCW signals is much more sensitive on movement of objects.
   Therefore, RadSee uses the phase of the demodulated FMCW signals.
3. RadSee can demodulate only the signal of the hand movement. This happens thanks to the FMCW signal modulation and high-directional patch antennas.
Also if the detection goes well, we still have 62 possible characters (26 letters + 26 capital letters + 10 numbers).
RadSee uses a **BiLSTM** (Bidirectional Short Term Memory) to classify handwriting characters.
In a phase data sequence RadSee analyze only the most relevant parts of the phase data sequence.
Since the writing activity consists in a coninuous phase movement for the writing hand, in practice each letter generates a unique temporal phase pattern.
For instance, we will be more interested in detecting the turning points more then the horizontal strokes. For this reason RadSee is focused on the BiLSTM model to pay attention to those important parts.

## Comparison
A table of comparison between RadSee and his competitors.
![[Pasted image 20250516195545.png]]

## Attack Model
We will now consider different attack scenario in which RadSee could be adopted.
- **Victim**: 
	- The victim is **writing** confidential informations on a paper or an electronic device (Ipad ecc.);
	- The victim is writing english **letters** and Arabic **numbers**;
	- The victim is facing **towards** the attacker;
- **Attacker**:
	- The attacker has **physical** **access** to space behind the wall;
	- The attacker has an approximate knowledge on victims **position** inside the room (can be extimated using radar signals);
	- There are no **RF-shielding materials** inside the wall between the victim and the attacker;

## Design analysis
FMCW radar is an active radio device uses frequency modulation to generate a continuous wave signal with a linear frequency sweep.
Radar antenna trasmit signal, and capture the signal **reflected** by the target.
The the **frequency** **difference** is analyzed (beat frequency) is proportional to target.
This frequence is analyzed over and over during time in order to set our radar parameters adaptively to the target distance and velocity.
![[Pasted image 20250516200846.png]]
Denoting $s_T(t)$ as the transmitting signal and $s_R(t)$ as the reflected signal, we have:$$s_T(t) = e^{j(2\pi f_0t + \pi Kt^2)}$$$$s_R = \alpha s_T(t-{2d\over c})$$
where $f_0$ is the starting frequency, $K$ is the frequency ramp rate, $\alpha$ is the path attenuation, $d$ is the distance from the radar to the target, and $c$ is the speed of light.
The 2 signals are mixed togheter and generates an Intermediate Frequence $IF$ as:$$s_{IF}(t) = s_T(t)s_R(t)^* = e^{(j4\pi K{d\over ct} + j4\pi f0{d\over c} - j4\pi K {d^2\over c^2})}$$
where the last exponentiation is negligible due to the square light speed, and the first and second part which represents the phase and the frequency of the signals are both in function of the distance.
We can extiamate the range and the velocity of the object as follows:
- **Range**: 
- **Velocity**:

						... Calculus here ...


