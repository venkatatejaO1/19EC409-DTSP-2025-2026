# EXP 1(C) : Analysis of audio signal for noise removal

# AIM: 

To analyse an audio signal and remove noise

# APPARATUS REQUIRED:  
PC installed with SCILAB. 

# PROGRAM: 
```
# ==============================
# AUDIO NOISE REMOVAL & SEPARATION
# ==============================

# Step 1: Install packages
!pip install -q librosa noisereduce soundfile

# Step 2: Upload clean and noise recordings
from google.colab import files
print("Upload clean/normal audio (speech/music)")
uploaded = files.upload()
clean_file = next(iter(uploaded.keys()))

print("Upload noise-only audio (background)")
uploaded = files.upload()
noise_file = next(iter(uploaded.keys()))

# Step 3: Load audios
import librosa, librosa.display
import numpy as np
import matplotlib.pyplot as plt
from IPython.display import Audio, display
import noisereduce as nr

clean, sr_c = librosa.load(clean_file, sr=None, mono=True)
noise, sr_n = librosa.load(noise_file, sr=None, mono=True)

# 🔧 Resample noise if sample rates differ
if sr_c != sr_n:
    print(f"Resampling noise from {sr_n} Hz → {sr_c} Hz")
    noise = librosa.resample(noise, orig_sr=sr_n, target_sr=sr_c)
    sr_n = sr_c

sr = sr_c
print(f"Clean audio SR = {sr_c}, Noise audio SR = {sr_n}")
print(f"Clean length = {len(clean)/sr:.2f} sec, Noise length = {len(noise)/sr:.2f} sec")

# Step 4: Make lengths equal (pad or cut noise)
if len(noise) < len(clean):
    reps = int(np.ceil(len(clean)/len(noise)))
    noise = np.tile(noise, reps)[:len(clean)]
else:
    noise = noise[:len(clean)]

# Step 5: Create noisy mixture
noisy = clean + noise * 0.5   # adjust noise scaling factor
print("Generated noisy signal.")

# Step 6: Play audio
print("\n--- Original Clean Audio ---")
display(Audio(clean, rate=sr))

print("\n--- Noise Sample ---")
display(Audio(noise, rate=sr))

print("\n--- Noisy (Merged) Audio ---")
display(Audio(noisy, rate=sr))

# Step 7: Frequency analysis (FFT spectra)
def plot_spectrum(signal, sr, title):
    n_fft = 2**14
    Y = np.fft.rfft(signal, n=n_fft)
    freqs = np.fft.rfftfreq(n_fft, 1/sr)
    magnitude = np.abs(Y)
    plt.figure(figsize=(12,4))
    plt.semilogy(freqs, magnitude+1e-12)
    plt.xlim(0, sr/2)
    plt.xlabel("Frequency (Hz)")
    plt.ylabel("Magnitude (log)")
    plt.title(title)
    plt.grid(True)
    plt.show()

plot_spectrum(clean, sr, "Spectrum of Clean Audio")
plot_spectrum(noise, sr, "Spectrum of Noise")
plot_spectrum(noisy, sr, "Spectrum of Noisy Audio")

# Step 8: Noise reduction (spectral subtraction)
reduced = nr.reduce_noise(y=noisy, y_noise=noise, sr=sr)

# Step 9: Separate estimated noise = noisy - reduced
estimated_noise = noisy - reduced

print("\n--- Denoised / Cleaned Audio ---")
display(Audio(reduced, rate=sr))

print("\n--- Extracted Noise Component ---")
display(Audio(clean, rate=sr))

# Step 10: Compare spectrograms
def plot_spec(signal, sr, title):
    D = librosa.stft(signal, n_fft=1024, hop_length=512)
    S_db = librosa.amplitude_to_db(np.abs(D), ref=np.max)
    plt.figure(figsize=(12,5))
    librosa.display.specshow(S_db, sr=sr, hop_length=512, x_axis="time", y_axis="hz")
    plt.colorbar(format="%+2.0f dB")
    plt.title(title)
    plt.ylim(0, sr/2)
    plt.show()

plot_spec(noisy, sr, "Spectrogram of Noisy Audio")
plot_spec(reduced, sr, "Spectrogram of Denoised Audio")
plot_spec(estimated_noise, sr, "Spectrogram of Extracted Noise")

```
# Original Clean Audio
[good-morning-242169.mp3](https://github.com/user-attachments/files/22458041/good-morning-242169.mp3)

# Noise Sample 
[intro-noise-131718.mp3](https://github.com/user-attachments/files/22458062/intro-noise-131718.mp3)

# Noisy (Merged) Audio 
[noisy merged audio.wav](https://github.com/user-attachments/files/22458077/noisy.merged.audio.wav)

# Extracted Noise removed
[Extracted Noise removed.wav](https://github.com/user-attachments/files/22458078/Extracted.Noise.removed.wav)

# OUTPUT: 

<img width="1012" height="393" alt="image" src="https://github.com/user-attachments/assets/2f2c4a77-405e-4ab9-a6a7-1cde32283ce4" />
<img width="1012" height="393" alt="image" src="https://github.com/user-attachments/assets/01a2a915-c29c-4d68-a0f3-1a86cae7e9ce" />
<img width="1012" height="393" alt="image" src="https://github.com/user-attachments/assets/b6669a16-0b8b-4e0d-bcaf-cf98965fc048" />
<img width="958" height="470" alt="image" src="https://github.com/user-attachments/assets/05e568fc-07b2-4cd9-904c-9bac663d3c97" />
<img width="958" height="470" alt="image" src="https://github.com/user-attachments/assets/6c83d31e-6a3d-4a0d-b9d0-8ff08465e6c1" />
<img width="958" height="470" alt="image" src="https://github.com/user-attachments/assets/92141e93-2b7e-42dd-b045-64d313848b38" />


# RESULT :

 Analysis of audio signal for noise removal is successfully executed in co lab

