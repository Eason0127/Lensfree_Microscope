<head>
  <script async src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
  <script>
    window.MathJax = {
      tex: {
        inlineMath: [['$', '$']],
        displayMath: [['\\[', '\\]'], ['$$', '$$']],
        processEscapes: true  // 允许 `$...$` 解析
      },
      svg: {
        scale: 1.2
      }
    };

    document.addEventListener("DOMContentLoaded", function() {
      MathJax.typesetPromise();
    });
  </script>
</head>




# Under-sampling simulation


## Sample 

It is a random generated sample with $1024 \times 1024$ resolution. I added some white Guassian noise to simulate the random noise while imaging. Due to in lensless microscope the FOV is equal to the active area on the sensor chip which is nearly equal to the size of the sample. So the **FOV** here is $204.8\mu m \times 204.8\mu m$ .

<div align="center">
    <figure>
        <img src="./under_sampling_pic/microscopic_sample_no_grid.png" width="400">
    </figure>
</div>

## Noise

In our set up, we will mainly have this four noise below:

Noise|Characteristic
---|---
Thermal noise|White noise
shot noise|White noise
Readout noise|Broadband noise
Dark current noise|Poisson distribution

1. **Thermal noise**
   
   It is caused by the random thermal motion of free electrons in the resistor element under the action of temperature. It can be described by Gaussian distribution $N(0,\sigma ^2)$ .

   $$\sigma = \sqrt{4k_bTRB}$$

2. **Shot noise**
   
   For example for LED or laser, the photons are generated by the energy level transition and this photon emission is random and in a short time it can be described by Poisson distribution.

   $$P(k)=\frac{\lambda^ke^{-\lambda}}{k!}$$

   $k$ is the number of photons that actually arrive, $\lambda$ is the number of photons that is expected to arrive. 
  
   This means that within a fixed exposure time, there will always be statistical fluctuations in the number of photons actually detected, and this fluctuation appears as noise. 

   This noice will become more prominent or dominant when the set up is under low light condition and short exposure time.

3. **Dark noise**
   
   It mainly comes from the carriers generated by thermal motion in semiconductor materials. Even in completely dark conditions, the sensor will detect a certain number of electrons.

   When under low light condition, it can be described by Poisson distribution while when we have large enough expected photon numbers, it can be described by Guassian distribution.

4. **Readout noise**
   
   Readout noise is from the electronic circuits inside and outside the sensor when transform the analog signal to digital signal. It will always exist at a fixed level. It can be also described by Guassian distribution.Under high light conditions, read noise is often no longer the dominant noise source

## Shannon sampling criteria

If we want to recover the signal without any loss we must obey the Shannon sampling criteria, so we can have the minimum sampling distance by as follows: 

$$K_{max}=\frac{2\pi}{\lambda_{min}}\rightarrow f_{max}=\frac{k_{max}}{2\pi}=\frac{1}{\lambda}\rightarrow \Delta x \leq \frac{\lambda_{min}}{2}$$

I assume we use the light of wavelength at $532nm$, so we can get **the minimum sampling distance** $\Delta x = 0.266 \mu m$ 

## Simulation method

I wanted to test the IPR reconstruction with the same sample under different pixel sizes but there would be a **difficulty** for code simulation.

    def angular_spectrum_method(field, pixelSize, distance, W, H, numPixels):
        GT = fftshift(fft2(ifftshift(field)))    ## The matrix size is 1024*1024
        transfer = Transfer_function(W, H, distance, 532e-9, pixelSize, numPixels)  ## The matrix size is 52*52
        transfer = resize_transfer_function(transfer, field.shape)
        gt_prime = fftshift(ifft2(ifftshift(GT * transfer)))  ## The matrix size is different so will cause error
        return gt_prime

<div style="display: flex; justify-content: center; align-items: center;">
    <figure style="margin: 10px; text-align: center;">
        <img src="./under_sampling_pic/说明图1.png" width="600">
    </figure>
    <span style="font-size: 40px; margin: 10px;">&harr;</span>
    <figure style="margin: 10px; text-align: center;">
        <img src="./under_sampling_pic/说明图2.png" width="600">
    </figure>
</div>


## Test 

**1. Ideal case with $0.2 \mu m$ pixel size**

$$
\begin{array}{c|c}
\textbf{RMS} & \textbf{SSIM} \\
\hline
2.47 \times 10^{-16} & 0.9999
\end{array}
$$

$$\text{RMS}=\sqrt{\frac{1}{n}\sum _{i=1}^n(\text{Reconstructed amp}_{i}-\text{Recorded amp}_i)^2}$$

, $i$ is the pixel index.

<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_11.png" width="500">
        <figcaption>Figure 1.1: Sample reconstructed</figcaption>
    </figure>
</div>


<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_1.png" width="400">
        <figcaption>Figure 1.2: Convergence figure</figcaption>
    </figure>
</div>

**2. Under-sampling with $0.8 \mu m$ pixel size**

$$
\begin{array}{c|c}
\textbf{RMS} & \textbf{SSIM} \\
\hline
2.63 \times 10^{-16} & 0.9999
\end{array}
$$

<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_22.png" width="500">
        <figcaption>Figure 2.1: Sample reconstructed</figcaption>
    </figure>
</div>

<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_2.png" width="400">
        <figcaption>Figure 2.2: Convergence figure</figcaption>
    </figure>
</div>



🚩**3. Under-sampling with $1.6 \mu m$ pixel size** (If we use IMX477 then this is the circumstance)

$$
\begin{array}{c|c}
\textbf{RMS} & \textbf{SSIM} \\
\hline
2.54 \times 10^{-16} & 0.9999
\end{array}
$$

<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_33.png" width="500">
        <figcaption>Figure 3.1: Sample reconstructed</figcaption>
    </figure>
</div>

<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_3.png" width="400">
        <figcaption>Figure 3.2: Convergence figure</figcaption>
    </figure>
</div>

**4. Under-sampling with $2.4 \mu m$ pixel size**

$$
\begin{array}{c|c}
\textbf{RMS} & \textbf{SSIM} \\
\hline
2.66 \times 10^{-16} & 0.9999
\end{array}
$$

<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_44.png" width="500">
        <figcaption>Figure 4.1: Sample reconstructed</figcaption>
    </figure>
</div>

<div align="center">
    <figure>
        <img src="./under_sampling_pic/Figure_4.png" width="400">
        <figcaption>Figure 4.2: Convergence figure</figcaption>
    </figure>
</div>

## Conclusion

I think the RMS error can't really reflect the reconstruction quality because the RMS errors of them are nearly the same but the ringing effect can be seen around the elements when we have under sampling and it becomes more and more serious as the undersampling becomes more severe. I'm looking for a parameter that can reasonably reflect the quality of the reconstruction.



<div style="display: flex; justify-content: center; align-items: center;">
    <figure style="margin: 10px; text-align: center;">
        <img src="./under_sampling_pic/Figure_11.png" width="600">
        <figcaption>Ideal case</figcaption>
    </figure>
    <figure style="margin: 10px; text-align: center;">
        <img src="./under_sampling_pic/Figure_22.png" width="600">
        <figcaption>0.8 micrometer</figcaption>
    </figure>
        <figure style="margin: 10px; text-align: center;">
        <img src="./under_sampling_pic/Figure_33.png" width="600">
        <figcaption>1.6 micrometer</figcaption>
    </figure>
        <figure style="margin: 10px; text-align: center;">
        <img src="./under_sampling_pic/Figure_44.png" width="600">
        <figcaption>2.4 micrometer</figcaption>
    </figure>
</div>

**What to do next:**

1. Find a good parameter to evaluate the reconstruction quality.
2. Test the quality after adding some random noise.