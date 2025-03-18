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

## Noise and undersampling simulation(18/03/2025)

The goal of this simulation is to try to figure out what would happen in our set up in which case we are going to have down-sampling(due to pixel size limitation) and noise issues. Also some metrics should be found to help us tell if the set up works as we expected.

---

### Review

1. **Downsampling**

> The maximum frequency that the sensor can detect below is replaced with $f_{max}$.
   
   Before the meeting(17/03/25), the way I simulate the downsampling is:

   1. Propagate the light from sample to sensor and get the hologram
   2. Down-sample the hologram. For example, if the pixel size ratio of image and sensor is 1:8 then I reserve the value of 1 pixel out of 8 pixels and do it in row and row. 
   3. Input the new down-sized hologram to do the reconstruction.
   
   Problem:

   I neglect $f_{max}$ and this may cause aliasing especially when high frequency is exsited in the image. So before I propagte the object field, I should first apply a fourier transform on the image and then filter its frequencies according to $f_{max}$. 



2. **Noise simulation**
   
I should mainly consider quantization noise and dark current noise.

### To do next

At first, I can just simply consider the noise as white Guassian noise. I will test how the reconstruction would be like with image of different level of filtering which means its maximum frequency can be $[f_{max}-\Delta,f_{max}+\Delta]$ and with different level of noise to see the influence of different SNR. I will try to give a reasonable range of noise and frequency (image) that can still get acceptable reconstruction results. I will use the datas to draw an image like the image below. Of course, it will be much more complex than this but just for example. 

<div align="center">
    <figure>
        <img src="./under_sampling_pic/heatmap.png" width="400">
    </figure>
</div>

**This is what I'm going to do:**
1. Filter the image to different maximum frequencies $[f_{max}-\Delta,f_{max}+\Delta]$ .
2. Do forward propagation based on the filtered image to get hologram1.
3. Add different level of white Guassian noise on hologram1 to get hologram2.
4. Down sample hologram2 according to the sensor to get hologram3.
5. Reconstruct the filtered image based on hologram3.
6. Collect datas.
   


