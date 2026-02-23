# PSK and QPSK
# Aim
Write a simple Python program for the modulation and demodulation of PSK and QPSK.
# Tools required
# Program
```asm
import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import butter, lfilter

def lpf(x, fc, fs):
    b, a = butter(4, fc/(0.5*fs), 'low')
    return lfilter(b, a, x)

fs, fc, br, T = 1000, 50, 10, 1
t = np.arange(0, T, 1/fs)
bd = fs // br

bits = np.random.randint(0,2,br)
msg = np.repeat(bits, bd)

carrier = np.sin(2*np.pi*fc*t)
psk = np.sin(2*np.pi*fc*t + np.pi*msg)

demod_psk = lpf(psk*carrier, fc, fs)
psk_dec = (demod_psk[::bd] < 0).astype(int)
psk_demod = np.repeat(psk_dec, bd)

qbits = np.random.randint(0,2,br)
if len(qbits)%2 != 0:
    qbits = np.append(qbits,0)

pairs = qbits.reshape(-1,2)
qmsg = np.repeat(qbits, bd)

mapping = {(0,0):0, (0,1):np.pi/2, (1,0):np.pi, (1,1):3*np.pi/2}
qpsk = np.zeros_like(t)

for i,p in enumerate(pairs):
    phase = mapping[tuple(p)]
    qpsk[i*2*bd:(i+1)*2*bd] = np.sin(2*np.pi*fc*t[i*2*bd:(i+1)*2*bd] + phase)

ci = np.cos(2*np.pi*fc*t)
cq = np.sin(2*np.pi*fc*t)

di = lpf(qpsk*ci, fc, fs)
dq = lpf(qpsk*cq, fc, fs)

dec = []
for i in range(len(pairs)):
    I, Q = di[i*2*bd], dq[i*2*bd]
    if I>0 and Q>0: dec += [0,0]
    elif I<0 and Q>0: dec += [0,1]
    elif I<0 and Q<0: dec += [1,0]
    else: dec += [1,1]

qpsk_demod = np.repeat(dec, bd)

plt.figure(figsize=(14,16))

plt.subplot(8,1,1); plt.plot(t,msg); plt.title("PSK Message")
plt.subplot(8,1,2); plt.plot(t,carrier); plt.title("PSK Carrier")
plt.subplot(8,1,3); plt.plot(t,psk); plt.title("PSK Modulated")
plt.subplot(8,1,4); plt.plot(t,psk_demod); plt.title("PSK Demodulated")

plt.subplot(8,1,5); plt.plot(t,qmsg); plt.title("QPSK Message")
plt.subplot(8,1,6); plt.plot(t,carrier); plt.title("QPSK Carrier")
plt.subplot(8,1,7); plt.plot(t,qpsk); plt.title("QPSK Modulated")
plt.subplot(8,1,8); plt.plot(t,qpsk_demod[:len(t)]); plt.title("QPSK Demodulated")

plt.tight_layout()
plt.show()
```
# Output Waveform
```
<img width="1389" height="1589" alt="image" src="https://github.com/user-attachments/assets/68abde5e-df0f-4a15-90df-13859853025e" />

```
# Results
```
Attach the output waveform
```
# Hardware experiment output waveform.
