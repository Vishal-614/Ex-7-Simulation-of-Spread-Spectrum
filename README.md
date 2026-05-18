# Ex: 07 - Simulation of Spread Spectrum Modulation
## AIM:
To simulate the process of Direct Sequence Spread Spectrum (DSSS) modulation using Binary Phase Shift Keying (BPSK).

## SOFTWARE REQUIRED:
Python IDE with numPy and Matplotlib.

## ALGORITHM:
Generate random binary data. Create a PN sequence of length 8. Perform BPSK modulation on the data (0 → -1, 1 → +1).
Spread the BPSK modulated signal using the PN sequence. Modulate the spread signal using a BPSK carrier. Plot the DSSS
spread signal and the BPSK modulated carrier waveform.

## PROGRAM:
```
import numpy as np
import matplotlib.pyplot as plt

# System Parameters
data_length = 4
chips_per_bit = 8
bit_rate = 1000
carrier_freq = 20000
sample_rate = 160000

chip_rate = bit_rate * chips_per_bit
samples_per_chip = int(sample_rate / chip_rate)

# Generate Random Data
data = np.random.randint(0, 2, data_length)

# Generate PN Sequence
pn_seq = np.random.choice([-1, 1], chips_per_bit)

print("Original Data Bits :", data)
print("PN Sequence :", pn_seq)

# BPSK Mapping
def bpsk_map(bit):
    return 2 * bit - 1

# DSSS Spreading
spread_signal = []

for bit in data:
    bpsk_bit = bpsk_map(bit)
    spread_signal.extend(bpsk_bit * pn_seq)

spread_signal = np.array(spread_signal)

# Carrier Modulation
chip_samples = np.repeat(spread_signal, samples_per_chip)

t = np.arange(len(chip_samples)) / sample_rate

carrier = np.cos(2 * np.pi * carrier_freq * t)

bpsk_waveform = chip_samples * carrier

# DSSS Demodulation
# 1. Multiply with local carrier (coherent demodulation)
demodulated_carrier = bpsk_waveform * carrier

# 2. Integrate over chip period and correlate with PN sequence
# Reshape demodulated_carrier into chips
demod_reshaped = demodulated_carrier.reshape(data_length * chips_per_bit, samples_per_chip)

# Average each chip (simulate integration)
integrated_chips = np.mean(demod_reshaped, axis=1)

# Correlate with PN sequence
received_bits = []
for i in range(data_length):
    # Extract chips for the current bit
    bit_chips = integrated_chips[i * chips_per_bit : (i + 1) * chips_per_bit]
    
    # Correlate with PN sequence
    correlation = np.sum(bit_chips * pn_seq)
    
    # Decision: if correlation > 0, it's 1; otherwise 0
    received_bits.append(1 if correlation > 0 else 0)

received_bits = np.array(received_bits)

print("\nReceived Data Bits :", received_bits)
print("Match with original data :", np.array_equal(data, received_bits))

# PLOTS
plt.figure(figsize=(12, 10))

# Plot 1: DSSS Spread Signal (Baseband)
plt.subplot(3, 1, 1)
plt.step(np.arange(len(spread_signal)) / chip_rate, spread_signal, where='mid')
plt.title("DSSS Spread Signal (Baseband)")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.grid(True)

# Plot 2: BPSK Waveform (Modulated with Carrier)
plt.subplot(3, 1, 2)
plt.plot(t, bpsk_waveform)
plt.title("BPSK Waveform (DSSS Modulated)")
plt.xlabel("Time (s)")
plt.ylabel("Amplitude")
plt.grid(True)
plt.xlim(0, data_length / bit_rate) # Show only a few bits

# Plot 3: Demodulated (Integrated and Correlated) Signal before Decision
plt.subplot(3, 1, 3)
# Create an x-axis for integrated chips based on chip rate
t_integrated = np.arange(len(integrated_chips)) / chip_rate
plt.plot(t_integrated, integrated_chips)
# Overlay lines for bit boundaries and correlation results
for i in range(data_length):
    bit_start_time = i / bit_rate
    plt.axvline(x=bit_start_time, color='r', linestyle='--', alpha=0.7, label='Bit Boundary' if i == 0 else '')
    
    # Indicate the decision point for each bit
    bit_mid_time = (i + 0.5) / bit_rate
    plt.axhline(y=0, color='gray', linestyle=':', alpha=0.7)
    plt.text(bit_mid_time, integrated_chips[i * chips_per_bit], f'Bit: {received_bits[i]}', color='purple', verticalalignment='bottom')


plt.title("Demodulated Signal (Integrated Chips)")
plt.xlabel("Time (s)")
plt.ylabel("Integrated Amplitude")
plt.grid(True)

plt.tight_layout()
plt.show()
```
## OUTPUT:
<img width="1127" height="1005" alt="Screenshot 2026-05-18 105729" src="https://github.com/user-attachments/assets/9c7dc374-8cc1-420e-93d1-f7a00654844f" />

## RESULT:
Thus, simulated the process of Direct Sequence Spread Spectrum (DSSS) modulation using Binary Phase Shift Keying (BPSK) successfully.
