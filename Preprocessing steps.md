
## Preprocessing Steps

The following steps are applied to the raw lung sound signals before feature extraction:

1.  **Resampling:**
    The raw lung sound signals, denoted as $LS_{raw}[n]$, are resampled to a frequency ($f_s$) of 4 kHz. Let the resampled signal be $LS_{re}[n]$.

2.  **Framing (Snippet Generation):**
    The resampled signal $LS_{re}[n]$ is framed into 5-second temporal snippets with a 50% overlap ($r=0.5$). The $t$-th snippet, $s_t[n]$, containing $N$ samples, is generated from $LS_{re}[n]$. If $u$ is the step size (samples corresponding to non-overlapped duration, e.g., 2.5s * 4kHz = 10000), the process can be expressed as:
    
    $s_t[n]$ = $LS_{re}[u \cdot (t-1) \cdot (1-r) + n]$,  $t$ = 1, 2,..., **T**
    
    
    where $T$ is the total number of snippets, and $n$ ranges from $0$ to $N-1$. 

4.  **Baseline Wandering (BW) Removal:**
    The BW component (typically 0-1 Hz) is removed using a DFT-based filtering technique.
    *   First, the Discrete Fourier Transform (DFT) of the $t$-th snippet $s_t[n]$ is calculated:
        
        $S_t[k]$ = $\sum_{n=0}^{N-1} s_t[n] e^{-j \frac{2\pi nk}{N}}$
        
    *   A thresholded DFT vector $\tilde{S_t}[k]$ is created by setting the DFT coefficients corresponding to the BW frequency range (0-1 Hz) to zero. If $k_{bw}$ is the index corresponding to ~1 Hz (calculated as $k_{bw} = \lfloor f_{bw} N / f_s \rfloor$ where $f_{bw}=1$ Hz), this involves zeroing out components $S_t[0]$ through $S_t[k_{bw}]$ (and potentially corresponding negative frequency components due to DFT symmetry).
      The text implies:

        $\tilde{S_t}[k]$ = $[0, \ldots, 0, S_t[k_{bw}+1], \ldots, S_t[N-k_{bw}-1], 0, \ldots, 0]$ 
    *   The BW-free time-domain snippet $s_{bwf}^t[n]$ is obtained by taking the Inverse DFT (IDFT) of $\tilde{S_t}[k]$:
        
        $S_{bwf}^t[n]$ = $\frac{1}{N} \sum_{k=0}^{N-1} \tilde{S_t}[k] e^{j \frac{2\pi nk}{N}}$
        

5.  **Amplitude Normalization:**
    The amplitude of each BW-free lung sound snippet $S_{bwf}^t[n]$ is normalized to the range [-1, 1]:
    
    $S_{norm}^t[n]$ = $\frac{s_{bwf}^t[n]}{\max(|s_{bwf}^t[n]|)}$
    
    
    where the max operation is performed over all samples $n$ within the snippet $t$.

---

*(The following steps describe the beginning of feature extraction, often considered part of the overall process)*

5.  **Short-Time Fourier Transform (STFT):**
    To extract spectral features, the STFT is computed from the normalized snippets $S_{norm}^t[n]$. The STFT $ST_{ls}[l, f]$ for frame $l$ and frequency $f$ is given by:
   
    
    $ST_{ls}[l, k]$ =$\sum_{n=0}^N_{win}-1 {S_norm}^t[n + lH] \cdot W[n] \cdot e^{-j \frac{2\pi nk}{N_{win}}} $
  
    *   Note:  The version above is a more standard STFT definition where $W[n]$ is a window function (e.g., Hamming window of length $N_{win}=1024$), $H$ is the hop length (e.g., 512 samples), $l$ is the frame index, and $k$ is the frequency bin index, a Hamming window `W[n]` of size 1024 and an overlap length `H` of 512 samples.

7.  **Mel Scale Conversion & Filterbank Application:**
    *   The Hertz frequencies ($f$) corresponding to the STFT bins are projected to the Mel scale ($f_{mel}$) using the formula:
        ```math
        f_{mel} = 2595 \cdot \log_{10}\left(1 + \frac{f}{700}\right)
        \tag{6}
        ```
    *   A Mel filterbank (e.g., 64 filters) is created based on the Mel scale.
    *   The Mel filterbank is applied to the magnitude (or power) of the STFT frames ($|ST_{ls}[l, k]|$ or $|ST_{ls}[l, k]|^2$) to produce Mel spectrogram features.

8.  **Log Transformation:**
    A logarithmic transform is applied to the amplitudes of the Mel spectrogram to produce the log-scaled Mel spectrogram, which often better represents perceived loudness and compresses dynamic range.

*(Optional Step mentioned)*
8.  **Colormap Conversion:**
    For visualization or use with certain image-based models, the 2D log-Mel spectrograms can be converted to 3-channel images using a colormap like "jet".
