# Delta-Modulation
## Aim:
 To demonstrate the principles of delta modulation (DM), a 1-bit differential pulse-code modulation (DPCM) technique, by analyzing its operation, identifying noise types (slope overload and granular noise), and exploring adaptive delta modulation (ADM) for noise minimization.
  
## Tools required:

  * Colab (for executing Python code)
  * Libraries: NumPy, Matplotlib
    
## Program:

~~~
#DELTA MODULATION 
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, filtfilt

# Parameters
fs = 10000  # Sampling frequency
f = 10  # Signal frequency
T = 1  # Duration in seconds
delta = 0.1  # Step size

t = np.arange(0, T, 1/fs)
message_signal = np.sin(2 * np.pi * f * t)  # Sine wave as input signal

# Delta Modulation Encoding
encoded_signal = []
dm_output = [0]  # Initial value of the modulated signal
prev_sample = 0

for sample in message_signal:
    if sample > prev_sample:
        encoded_signal.append(1)
        dm_output.append(prev_sample + delta)
    else:
        encoded_signal.append(0)
        dm_output.append(prev_sample - delta)
    prev_sample = dm_output[-1]

# Delta Demodulation (Reconstruction)
demodulated_signal = [0]
for bit in encoded_signal:
    if bit == 1:
        demodulated_signal.append(demodulated_signal[-1] + delta)
    else:
        demodulated_signal.append(demodulated_signal[-1] - delta)

# Convert to numpy array
demodulated_signal = np.array(demodulated_signal)

# Apply a low-pass Butterworth filter
def low_pass_filter(signal, cutoff_freq, fs, order=4):
    nyquist = 0.5 * fs
    normal_cutoff = cutoff_freq / nyquist
    b, a = butter(order, normal_cutoff, btype='low', analog=False)
    return filtfilt(b, a, signal)

filtered_signal = low_pass_filter(demodulated_signal, cutoff_freq=20, fs=fs)

# Plotting the Results
plt.figure(figsize=(12, 6))

plt.subplot(3, 1, 1)
plt.plot(t, message_signal, label='Original Signal', linewidth=1)
plt.legend()
plt.grid()

plt.subplot(3, 1, 2)
plt.step(t, dm_output[:-1], label='Delta Modulated Signal', where='mid')
plt.legend()
plt.grid()

plt.subplot(3, 1, 3)
plt.plot(t, filtered_signal[:-1], label='Demodulated & Filtered Signal', linestyle='dotted', linewidth=1, color='r')
plt.legend()
plt.grid()

plt.tight_layout()
plt.show()
~~~

## Output Waveform:

![image](https://github.com/user-attachments/assets/5809e659-66c0-46f5-9af0-1a5177096204)

## Results:

 Delta Modulation produced a staircase approximation of the input signal, with slope overload and granular noise observed due to improper step size. Adaptive Delta Modulation reduced noise by dynamically adjusting the step size, improving signal reconstruction quality.

