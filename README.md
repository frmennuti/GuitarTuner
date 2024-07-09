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
- user needs to be notified with a new value of the tone played at least each 200ms

The solution is to set up the microcontroller to sample with a sampling frequency of 5120 Hz, and use DMA buffer of length 2048 elements. In this way the buffer will be filled each 400ms, meaning that the interrupt of buffer full will be called each 400ms and the one of half full buffer will be called each 200ms.
The fft can be performed on a rolling buffer of 2048 elements (improve efficiency) each time 1024 elements are acquired and after having discarded the older 1024 elements.
