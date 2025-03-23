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

- [Lensless Microscope introduction](#lensless-microscope-introduction)
  - [Hologram](#hologram)
  - [Angular spectrum method](#angular-spectrum-method)
  - [Iterative phase retrieval algorithm](#iterative-phase-retrieval-algorithm)
  - [Twin image and artifact](#twin-image-and-artifact)
  - [Shannon sampling criteria](#shannon-sampling-criteria)
  - [Noise issue](#noise-issue)
- [Sampling simulation with noise](#sampling-simulation-with-noise)
  - [Phase 1](#phase-1)


# Lensless Microscope introduction


## Hologram



## Angular spectrum method

This is the method we use to simulate how the light propagates from the sample plane to sensor plane. In the IPR algorithm, we need to use angular spectrum method to simulate both forward and backward propagation multiple times. So it's very frequenctly used.

 Let the complex field across that $z=0$ plane be represented by $U(x,y,0)$ . The goal is to calculate the resulting field $U(x,y,z)$ that appears across some time which is parallel to the first plane with a distance $z$ .At $z = 0$ , The function $U(x,y,0)$ is given by

 $$A(f_x,f_y;0)=\iint _{-\infty}^{\infty}U(x,y,0)\exp[-j 2\pi(f_xx+f_yy)]dxdy$$

 $U(x,y,z)$ can be wrote as

  $$U(x,y,0)=\iint _{-\infty}^{\infty}A(f_x,f_y;0)\exp[j 2\pi(f_xx+f_yy)]dxdy$$

The plane wave can be described in a complex form:

$$p(x,y,z;t)=\exp[j(\vec{k}\cdot\vec{r}-2\pi v t)]$$

where $\vec{r}=(x,y,z)$, $\vec{k}=\frac{2\pi}{\lambda}(\alpha, \beta, \gamma)$, in which $\alpha ^2 + \beta ^2 + \gamma ^2 = 1$ . So if we write $A(f_x,f_y;z)$ in this complex form, it will turn to be 

$$A(f_x,f_y;z)=A(\frac{\alpha}{\lambda},\frac{\beta}{\lambda};z)=\iint _{-\infty}^{\infty}U(x,y,z)\exp[-j 2\pi(\frac{\alpha}{\lambda}x+\frac{\beta}{\lambda}y)]dxdy$$

then we have 

  $$U(x,y,z)=\iint _{-\infty}^{\infty}A(\frac{\alpha}{\lambda},\frac{\beta}{\lambda};z)\exp[j 2\pi(\frac{\alpha}{\lambda}x+\frac{\beta}{\lambda}y)]d\frac{\alpha}{\lambda}d\frac{\beta}{\lambda}$$

In addition, $U$ must satisfy the Helmholtz equation $\nabla^2U+k^2U=0$ at all source-free points. At this case, we only consider the $z$ direction, so we can write the solution like this

$$A(x,y,z)=A(x,y,0)\exp{(j\frac{2\pi}{\lambda}\sqrt{1-\alpha^2-\beta ^2}z)}$$

As a result, we must have $\alpha^2 + \beta ^2 <1$ or $f_x^2 + f_y ^2< \lambda ^2$, otherwise $z$ will only have imaginary solution and this wave can't propagate at far field and will attenuate to a level that is almost negligible within a certain distance. **So that is our support area in frequency domain.**

Then we replace $A(x,y,z)$ with the Helmholtz equation's solution, we have

  $$U(x,y,z)=\iint _{-\infty}^{\infty}A(\frac{\alpha}{\lambda},\frac{\beta}{\lambda};0)\exp{(j\frac{2\pi}{\lambda}\sqrt{1-\alpha^2-\beta ^2}z)}\exp[j 2\pi(\frac{\alpha}{\lambda}x+\frac{\beta}{\lambda}y)]d\frac{\alpha}{\lambda}d\frac{\beta}{\lambda}$$

or we can write in a more simple form:

$$U(x,y,z)=\mathcal{F}^{-1}\{\mathcal{F}\{U(x,y,0)\}\cdot H(x,y,z)\}$$

**Code Simulation:**

In practice, it's not so hard to simulate the angular spectrum method. The only difficulty is to calculate the frequency index. The frequency index can be calculated by 

$$f_x=\frac{k_x}{\Delta x},f_y=\frac{k_y}{\Delta y}$$

$k_x,k_y$ is the index of the discretized grid and $\Delta x, \Delta y$ is the spatial sampling distance of the sensor. This can be written more detailly as 

$$f_x = \frac{x-\frac{N}{2}-1}{N\times \Delta x},f_y = \frac{y-\frac{N}{2}-1}{N\times \Delta y}$$

$N$ is the number of the pixels, $x,y$ is the spatial coordination without centralization. If the coordination is already centralized then it can be simplified as

$$f_x = \frac{W}{N\Delta x},f_y = \frac{H}{N\Delta y}$$

$W,H$ is the centralized spatial grid index. Then all the work remained is to write the codes according to the angular spectrum method calculation process. The whole process is shown below.

    def Transfer_function(W, H, distance, wavelength, pixelSize, numPixels):
        FX = W / (pixelSize * numPixels) # Calculate the index of frequency grid
        FY = H / (pixelSize * numPixels) # Calculate the index of frequency grid
        k = 2 * np.pi / wavelength 
        square_root = np.sqrt(1 - (wavelength ** 2 * FX ** 2) - (wavelength ** 2 * FY ** 2))
        valid_mask = (wavelength ** 2 * FX ** 2 + wavelength ** 2 * FY ** 2) <= 1
        square_root[~valid_mask] = 0
        temp = np.exp(1j * k * distance * square_root)
        return temp

    def angular_spectrum_method(field, pixelSize, distance, W, H, numPixels):
        GT = fftshift(fft2(ifftshift(field)))
        transfer = Transfer_function(W, H, distance, 532e-9, pixelSize, numPixels)
        gt_prime = fftshift(ifft2(ifftshift(GT * transfer)))
        return gt_prime


## Iterative phase retrieval algorithm

## Twin image and artifact

## Shannon sampling criteria

## Noise issue

In practice, the detection will definitly include noise and this will influence the reconstruction result in a negative way. The following types of noise is very commen.

1. **Shot noise and dark current noise**
    The generation of photons and electrons is discrete and random, when you want to discretize the signal, the number of photons or electrons detected at a given time is uncertain and follows a Poisson distribution. If the exposure time is long enough, the Poisson distribution can be approximated by a normal distribution. So both the noise from photon arrival and dark current generation are types of shot noise. They differ only in their origin. The former is due to the incoming light, and the latter is due to thermal effects within the sensor when don't have illlumination (the heat will also cause energy transition even without light and hense generate electrons).

    **Code simulation**
    Before the simulation, we should assume the full well capacity


2. **Quantization noise**
   Quantization noise is the error generated in the process of converting a continuous analog signal to a discrete digital signal. When the ADC (analog-to-digital converter) maps a continuous signal to a finite number of discrete levels, some subtle changes in the original signal are lost, and this error is called quantization noise.

   **Calculation**

   If the bit depth of the sensor is B, then 

---

# Sampling simulation with noise

The goal of this simulation is to try to figure out what would happen in our set up in which case we are going to have down-sampling(due to pixel size limitation) and noise issues. Also some metrics should be found to help us tell if the set up works as we expected.

## Phase 1

At this stage, the noise would be simplified as a white Guassian noise. When this simulation is done, I would then go deeper in the noise simulation considering different ratio of different noises. 

In phase 1, the following work is done:

1. Filtering the original image according to the cut-off frequency $f_{max}$ based on the sensor according to the Shannon sampling criteria.
2. Apply forward-propagation on the filtered image to get the hologram.
3. Sample the hologram field.
4. Add different levels of noise on the sampled hologram to simulate the real life case.
5. Reconstruct the image based on the IPR algorithm and collect datas.
6. Change the cut-off frequency $f_{cut} \in [f_{max}, f_{max} + \Delta]$ and repeat step 2 ~ 5 to analyze the reconstruction when it is under under-sampling. 

**First** I want to show the reconstruction results without any noise but with cut-off frequencies from $f_{max}$ to $f_{max} + \Delta$ to see the reconstructions when having different levels of down-sampling. In the image below, on the left is the original image and on the right is the filtered image. As can be seen, the amplitude of the filtered image is smaller because it loses some energy when filtering and the edges of the small circles are not as sharp as the original image. This is also due to filtering, it loses the high frequency parts and hence loses some details.

> There would be many filtered image based on different cuf-off frequencies and this one is just for example.

<div style="display: flex; justify-content: space-between;">
  <div style="flex: 1; padding: 5px;">
    <img src="./reportM1_pic/样本二原图.png" alt="图片1" style="width: 100%;">
  </div>
  <div style="flex: 1; padding: 5px;">
    <img src="./reportM1_pic/样本二.png" alt="图片2" style="width: 100%;">
  </div>
</div>

This image below shows a decrease in SSIM when the cut-off frequency goes larger and larger. $f_0$ is the maximum frequency that the sensor can detect which is $f_{max}$. With the index increasing by $1$, the cut-off frequency increases by $10\%$ . For example, $f_2$ is $120\%$ of $f_0$ . The SSIM metric decreases because the additional frequencies beyond $f_0$ are not present in the captured data. Thus, including them (or attempting to reconstruct them) introduces artifacts and aliasing, degrading the overall reconstruction quality. The SSIM does not change since $f_4$ because the maximum frequency of the image is between $f_3$ and $f_4$. Therefore, even if the cutoff frequency is increased, there will be no change because all the frequencies of the image have been included. This is in line with the expectation.

<div align="center">
    <figure>
        <img src="./reportM1_pic/复杂样本无噪声SSIM.png" width="500">
    </figure>
</div>

In conclusion:

- **At $f_0$**, the cutoff frequency matches the sensor’s maximum frequency so only valid, alias-free frequencies are included.
- **From $f_0$ to $f_4$**, increasing the cutoff gradually introduces frequencies that the sensor could not detect, which degrades reconstruction quality as measured by SSIM.
- **Beyond $f_4$**, SSIM plateaus because the maximum frequency of the image is already smaller than the cut-off frequency and keep increasing the cut-off value won't introduce any undetected information so the SSIM plateaus.

I can give more examples, below is the another filtered image and it's SSIM curve. The result is basically the same.



<div align="center">
    <figure>
        <img src="./reportM1_pic/样本1过滤图.png" width="500">
    </figure>
</div>

<div align="center">
    <figure>
        <img src="./reportM1_pic/样本无噪声SSIM.png" width="500">
    </figure>
</div>

**Then**, still use the first sample shown before but this time noise is added. The test results are shown in the heat map below. It can be concluded that when noise is added to the same sample, for any cutoff frequency, having a higher SNR consistently has a better reconstruction. In other words, noise level becomes the dominant factor: even if you optimize the cutoff frequency, excessive noise can still degrade the image quality significantly. Conversely, if noise is minimal (i.e., SNR is high), the reconstruction tends to remain relatively good even if the cutoff frequency is not perfectly set. Hence, in this scenario, the influence of noise on the reconstructed image's quality outweighs that of the cutoff frequency.

<div align="center">
    <figure>
        <img src="./reportM1_pic/样本二热图.png" width="800">
    </figure>
</div>

The following picture can give you a more intuitive feeling of the impact of SNR on reconstruction. The SNR of the left and right image is 43 dB and 19 dB separately. Their corresponding SSIM is around 0.7 and 0.5 respectively. [The original image is shown before you can click here to check.](./reportM1_pic/样本二原图.png)



<div style="display: flex; justify-content: space-between;">
  <div style="flex: 1; padding: 5px;">
    <img src="./reportM1_pic/样本二43dB.png" alt="图片1" style="width: 100%;">
  </div>
  <div style="flex: 1; padding: 5px;">
    <img src="./reportM1_pic/样本二19dB.png" alt="图片2" style="width: 100%;">
  </div>
</div>

I think we can use SSIM and RMS to analyze the quality of reconstruction. RMS can measure the overall quality of reconstruction, while SSIM can better reflect the reconstruction quality of details and is more in line with our eye's intuitive feeling. However, to use SSIM, we need a traditional microscope to image the sample to obtain the "real image" we need.