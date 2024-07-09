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

The solution is to set up the microcontroller to sample with a sampling frequency of 5120 Hz, and use DMA buffer of 1024 elements. In this way the buffer will be filled each 200ms, meaning that the interrupt of buffer full will be called each 200ms.

The number of elements in the DMA buffer is not enough to satisfy the frequency resolution stated above, therefore a second rolling buffer will be instantiated and handled with FIFO logic so that the fft will be performed on a rolling buffer of 2048 elements meeting all the requirements.
The fft can be performed on a rolling buffer of 2048 elements (improve efficiency) each time 1024 elements are acquired and after having discarded the older 1024 elements.

Performing fft on a 2048 elements array sampled with fs=5120Hz will provide us 1024 bins up to the fs/2=2560Hz component, therefore each bin will have resolution 2560/1024=2,5Hz
