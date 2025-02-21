<head>
  <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
  <script type="text/x-mathjax-config">
    MathJax.Hub.Config({
      tex2jax: {
      skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
      inlineMath: [['$','$']]
      }
    });
  </script>
</head>

# Under-sampling simulation

## Sample 

It is a random generated sample with $1024 \times 1024$ resolution. I added some white Guassian noise to simulate the random noise while imaging. Due to in lensless microscope the FOV is equal to the active area on the sensor chip which is nearly equal to the size of the sample. So the **FOV** here is $204.8\mu m \times 204.8\mu m$ .

<div align="center">
    <figure>
        <img src="./under_sampling_pic/microscopic_sample_no_grid.png" width="400">
        <figcaption>Figure 1: Sample</figcaption>
    </figure>
</div>

## Shannon sampling criteria

If we want to recover the signal without any loss we must obey the Shannon sampling criteria, so we can have the minimum sampling distance by as follows: 

$$K_{max}=\frac{2\pi}{\lambda_{min}}\rightarrow f_{max}=\frac{k_{max}}{2\pi}=\frac{1}{\lambda}\rightarrow \Delta x \leq \frac{\lambda_{min}}{2}$$

I assume we use the light of wavelength at $532nm$, so we can get **the minimum sampling distance** $\Delta x = 0.266 \mu m$

## Simulation method



## Test 1 with 5mm sample-sensor distance

**1. Ideal case with $0.2 \mu m$ pixel size**

$$
\begin{array}{c|c}
\textbf{RMS} & \textbf{SSIM} \\
\hline
2.5 \times 10^{-16} & 0.9999
\end{array}
$$



<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_1 rec.png" width="800">
        <figcaption>Figure 2: Sample reconstructed</figcaption>
    </figure>
</div>

<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_1.png" width="500">
        <figcaption>Figure 3: Convergence figure</figcaption>
    </figure>
</div>

**2. Under-sampling with $1.6 \mu m$ pixel size**

$$
\begin{array}{c|c}
\textbf{RMS} & \textbf{SSIM} \\
\hline
2.78 \times 10^{-16} & 0.9999
\end{array}
$$

🚩