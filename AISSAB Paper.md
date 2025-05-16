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
   However, the phase of demodulated FMCW signals is much more sensitive on movements.