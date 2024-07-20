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

The solution is to set up the microcontroller to sample with a sampling frequency of 5120 Hz, and use DMA buffer of 2048 elements. In this way the buffer will be filled each 400ms, meaning that the interrupt of buffer full will be called each 400ms.

The system shall be able to communicate (which is the best solution? UART? USB? TBD) to the user the evaluated tone more than 3 times per second so that the user is constantly prompted the most recent played tone. Evaluating the fft each 400ms is not aligned with such requirement, therefore the half complete callback is exploited. Such callback is called each 200ms. Each half complete callback and complete callback a fft shall be triggered and result shall be communicated to the user.

Each time 1024 samples gets acquired (alternatively halfcomplete and complete callbacks) the first and last 1024 samples will be copied to the rolling 2048 buffer over which the fft will be evaluated.

This will ensure responsiveness requirement and the resolution requirement since performing fft on a 2048 elements array sampled with fs=5120Hz will provide us 1024 bins up to the fs/2=2560Hz component, therefore each bin will have resolution 2560/1024=2,5Hz

SW Architecture:
FreeRTOS is used as a Real Time Operating System.
Two tasks are instantiated: DMATask and fftTask
- DMATask gets notified by the ISR (HalfCplt e ConvCplt), once the notification is received the corresponding last 1024 samples are copied from the adc_buffer used by the DMA to the "parkbuffer", once done that the task notifies the ffttask
- fftTask shall copy the newer 1024 samples from the park buffer to the buffer of 20048 element used for the fft algoorithm calculation, cast them from uint32 to float, discard the oldest 1024 elements in the buffer and then trigger a fft evaluation

