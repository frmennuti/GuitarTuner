# GuitarTuner
Develop STM32 based guitar tuner

E2: 82.41 Hz
A2: 110 Hz
D3: 146.8 Hz
G3: 196 Hz
B3: 246.9 Hz
E4: 329.6 Hz

Microphone: https://www.adafruit.com/product/1063

Microcontroller: STM32F303 Nucleo board

Fourier Transform:
- maximum frequency to be sampled and recognized is 329.6 Hz (E4)
- fft resolution should be of at least 3Hz (more or less 10 bins between the 2 tones nearer to each other: E2 and A2)
