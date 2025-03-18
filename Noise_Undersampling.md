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

## Noise and undersampling simulation

### Review

1. **Downsampling**
   Before the meeting(17/03/25), the way I simulate the downsampling is:

   1. Propagate the light from sample to sensor and get the hologram
   2. Down-sample the hologram. For example, if the pixel size ratio of image and sensor is 1:8 then I reserve the value of 1 pixel out of 8 pixels and do it in row and row. 
   3. Input the new down-sized hologram to do the reconstruction.
   
   Problem:

   I neglect that in reality the field will always be continuous. I don't need to care about the dimension of the image but its frequencies. If I don't want down sampling, I can just filter the frequencies that are higher than the maximum frequency that the sensor can detect. If I want to simulate downsampling, I can control the filter to make sure some of the frequencies are higher than the maximum frequency that the sensor can detect. This way is more general and easy to operate and I don't need to care about the dimension problem.

<span style="color:red">Just 1 concern: 
If I filter the image, should I compare the reconstruction result with the image filtered or the original image?(I think it's the filtered image.) For example, If I want to simulate the reconstruction that obey the Shannon sampling criteria, I should filter the image to make sure the frequencies are all lower than the maximum frequence the sensor can detect and based on that filtered image, I get the reconstruction and which I should compare with? The filtered image or the original image?
</span>



