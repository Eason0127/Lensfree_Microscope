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

In practice, it's not so hard to simulate the angular spectrum method. The only difficulty is to calculate the frequency index. As 

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

## Noise issue

In practice, the detection will definitly include noise and this will influence the reconstruction result in a negative way.

# Sampling simulation with noise

The goal of this simulation is to try to figure out what would happen in our set up in which case we are going to have down-sampling(due to pixel size limitation) and noise issues. Also some metrics should be found to help us tell if the set up works as we expected.

---

## Phase 1




