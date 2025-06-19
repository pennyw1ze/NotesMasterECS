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
   - Range resolution:$$R = {c\tau\over 2},\tau = {1\over B},R = {c\over 2B};$$
   where $c$ is the speed of light and $B$ is the bandwith of the antennas.
   To achieve range resolution of $1mm$ it requires $$B = {3\times 10^8\over 2\times 10^{-3}} = 150GHz;$$
   which is impossible in practice.
   However, the phase of demodulated FMCW signals is much more sensitive on movement of objects:$$\phi = {4\pi R\over \lambda},\ \Delta \phi = {4\pi \Delta R\over \lambda};$$
   where $\lambda$ is the wave length of the signal.
   Therefore, RadSee uses the phase of the demodulated FMCW signals.
3. RadSee can demodulate only the signal of the hand movement. This happens thanks to the FMCW signal modulation and high-directional patch antennas.
	- FMCW signal is transformed trougth a Fourier transform function which divides the signal in bin, small intervals which corresponds to different distances. This is possible thanks to the FFT that transport the signal from the frequency domain to the space domain. In this way RadSee can analyze only the bin corresponding to the hand distance. A bin containing a spike correspond to an object relevation;
	- Thanks to highly directional patch antennas RadSee is able to ignore signals from other direction except a small zone to which he is pointing to; 

Also if the detection goes well, we still have 62 possible characters (26 letters + 26 capital letters + 10 numbers).
RadSee uses a **BiLSTM** (Bidirectional Short Term Memory) to classify handwriting characters.
LSTM are a kind of neural network designed to analyze data streams (words, texts or pahse sequences signals).
LSTM has a memory that allows them to remember important past informations and forget the usless one. This is really important when data has a time sequenced structure.
The BiLSTM has 2 LSTM, one of them read the sequence forward from start to end, and the other one reads the same sequence in the reverse order.
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
- **Range**: we use FFT operation to conver the chirp in the frequency domain and we apply the previous formula:$$\Delta d = {c\over 2B};$$
  where $B$ is the bandwidth;
- **Velocity**: Suppose that the time duration of one chirp is $T$ and that the size of the FFT is $M$: in this case, a peak is identified at the $k$th FFT bin. We will have:$$\Delta v = {c\over 2MTf_0};$$
  determined by the initial frequency $f_0$, the number of chirps $M$ and the time duration of a chirp $T$.

When the object moves 1 mm, RadSee will observe ${2df_0\over c} 2π = 0.25$ radian (about 14◦) phase change on the corresponding Range-FFT bin. Typically, handwriting movement is larger than 5 mm, which will generate 70◦ phase change on the Range-FFT bin. Therefore, the radar will measure the phase pattern over time when a victim is writing, and use the temporal phase pattern to classify the letters being written.
The static objects detected by RadSee will not cause any trouble because their reflective signal will appear in our reflected signal as a constant component which can be easily removed or adjusted.