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

- [Set-up resolution](#set-up-resolution)
- [USAF](#usaf)


---

<span style="color:red;">Question 1</span>

- "It is necessary that the scattered wave be **weak** compared with the reference wave, which restricts this simple form of holographic reconstruction to relatively **sparse samples**." ---Orzcan

<span style="color:Blue;">Understanding for Q1</span>

$$|R+O| ^2 = |R|^2+|O|^2+R^*O+RO^*$$


- For weak $|O|^2$ reason:
  We can take $|R|^2$ as a DC noise and $|O|^2$ can be taken as weak noise. We can get the pure object information by filtering the $|R|^2$ and just simply ignore the weak noise $|O|^2$ . If it's too strong, it's hard to extract the useful obeject information.
- For sparse reason:
  It's not suitable for sparse sample because it may cause large amount of scattered object light and interfere with each other. At last, this will cause a strong self-interference term $|O|^2$ . Beside that, sparse sample may make the phase information more complex due to the object light might be scatterd and go through other objects and resultly have a phase superposition.
  
<span style="color:red;">Question 2</span>

- “Disturbances in the electric field with spatial frequency components greater than 1/λ resulting from fine spatial features in the object will evanescently decay.”
  $$|K_x|\leq \frac{2\pi}{\lambda}, f_x = \frac{2\pi}{\lambda _x} \rightarrow f_x \leq \frac{1}{\lambda}$$

- "Only those disturbances with characteristic size greater than approximately λ/2 will propagate to the far field."
  It means $f_x < \frac{2}{\lambda}$ .Quite strange...

---

理论上讲我的分辨率最高可以达到2.4微米

## Set-up resolution

**The resolution**:

$$\Delta x = \frac{\lambda}{2\text{NA}}=\frac{\lambda}{2n\sin \theta _{max}}$$

**The temporal coherence length**:

$$L_{coh}=(\frac{2\ln 2}{\pi})\frac{\lambda ^2}{n\Delta \lambda}$$

**The maximum scattering angle**:

$$\theta _{max} \leq \text{arccos} (\frac{z_2}{z_2 + L _{coh}})$$

> This is the resolution limitation imposed by temporal coherence.

$$\theta _{max} \leq \arctan (\frac{0.61 \lambda z_1 /D}{z_2})$$

> D is the lateral size of the light source.

For example: 

- Wavelength: $532$ nm
- $z_2 = 1$ mm 

If I want to have an equivalent effect with 0.8 NA microscope objective, then

$$\sin \theta _{max}=0.8 \rightarrow \theta_{max}= 53.13\degree$$

$$\arccos (\frac{1\times 10^{-3}}{1\times 10^{-3} + L _{coh}})=53.13\degree\rightarrow L_{coh} \geq 0.00066m$$

$$(\frac{2\ln 2}{\pi})\frac{\lambda ^2}{n\Delta \lambda} \geq 0.00066 \rightarrow \Delta \lambda \leq 1.89 \times 10^{-10} m$$

$$\arctan (\frac{0.61 \times 532 \times 10^{-9} \times z_1 / D}{1 \times 10^{-3}})\geq53.13\degree \rightarrow D/z_1 \leq 2.4 \times 10 ^{-4}$$

> In Orzcan's paper, their $z_2 = 0.1 mm$ , so $\Delta \lambda \leq 1.6 \times 10^{-9}$ .

---

## USAF

- If we choose $1.2 \mu m$ pixel size, theoretically the maximum resolution we can achieve is $2.4 \mu m$ .

**What's the process?**

1. <span style="color:purple;">Set the parameter and read the USAF test chart</span>
   - sample-sensor distance $1 mm$
   - sensor pixel size $1.2 \mu m$
   - image pixel size $0.2, 0.4, 0.6 \mu m$
2. <span style="color:purple;">Define the sample field</span>
   - Amplitude absorption $am = \exp (-2 \times grayscale_{value})$ and phase delay $phase = 3 \times grayscale_{value}$
   - Filter the sample field based on $k_x^2 + k_y^2 \leq k^2\rightarrow f_x \leq \frac{1}{\lambda}$ . Because the wave of frequency that higher than this frequency will evanescently decay and can't finally reach the sensor.

3. <span style="color:purple;">Acquire the hologram</span>
   - Forward propagate to get the hologram

4. <span style="color:purple;">Sampling</span>
   - Point-by-point sampling according to the pixel ratio of image and sensor. For example, if the image pixel is $0.4\mu m$ and the sensor pixel is $1.2\mu m$ then It should take 1 pixel out of 3 pixels.

5. <span style="color:purple;">Reconstruction</span>
   - Input the sampled hologram to the IPR function-using the sensor pixel

**Experiment 1**




