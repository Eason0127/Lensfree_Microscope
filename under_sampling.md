<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Lensfree Microscope</title>

  <!-- MathJax 解析数学公式 -->
  <script async src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
  
  <script>
    document.addEventListener("DOMContentLoaded", function () {
      // 确保 MathJax 解析数学公式
      if (window.MathJax) {
        MathJax.typesetPromise();
      }

      // 自动生成目录
      let toc = document.getElementById("toc");
      toc.innerHTML = ""; // **清空目录，防止重复生成**

      let headers = document.querySelectorAll("h2, h3"); // 只获取 h2 和 h3
      headers.forEach(header => {
        let id = header.id || header.textContent.trim().replace(/\s+/g, "-").toLowerCase();
        header.id = id;

        let li = document.createElement("li");
        li.innerHTML = `<a href="#${id}">${header.textContent}</a>`;
        toc.appendChild(li);
      });
    });
  </script>

  <style>
    body {
      display: flex;
      margin: 0;
      font-family: Arial, sans-serif;
    }
    #sidebar {
      position: fixed;
      left: 0;
      top: 0;
      width: 200px;
      height: 100vh;
      background-color: #f9f9f9;
      padding: 15px;
      overflow-y: auto;
      box-shadow: 2px 0 5px rgba(0, 0, 0, 0.2);
    }
    #sidebar h2 {
      font-size: 18px;
      margin-bottom: 10px;
    }
    #sidebar ul {
      list-style: none;
      padding: 0;
    }
    #sidebar ul li {
      margin: 10px 0;
    }
    #sidebar ul li a {
      text-decoration: none;
      color: #007bff;
      font-weight: bold;
    }
    #sidebar ul li a:hover {
      text-decoration: underline;
    }
    #content {
      margin-left: 220px;
      padding: 20px;
      width: calc(100% - 220px);
    }
  </style>
</head>

<body>

<!-- 侧边栏 -->
<div id="sidebar">
  <h2>目录</h2>
  <ul id="toc"></ul>
</div>

<!-- 主要内容 -->
<div id="content">
  <h1 id="under-sampling-simulation">Under-sampling simulation</h1>

  <h2 id="sample">Sample</h2>
  <p>It is a random generated sample with $1024 \times 1024$ resolution...</p>

  <h2 id="shannon-sampling-criteria">Shannon sampling criteria</h2>
  <p>If we want to recover the signal without any loss...</p>

  <h2 id="simulation-method">Simulation method</h2>
  <p>I wanted to test the IPR reconstruction...</p>

  <div style="display: flex; justify-content: center; align-items: center;">
    <figure style="margin: 10px; text-align: center;">
        <img src="./under_sampling_pic/说明图1.png" width="600">
    </figure>
    <span style="font-size: 40px; margin: 10px;">&harr;</span>
    <figure style="margin: 10px; text-align: center;">
        <img src="./under_sampling_pic/说明图2.png" width="600">
    </figure>
  </div>

  <h2 id="test">Test</h2>
  <p><strong>1. Ideal case with $0.2 \mu m$ pixel size</strong></p>

  <h2 id="conclusion">Conclusion</h2>
  <p>Here we summarize the results...</p>
</div>

</body>
</html>




# Under-sampling simulation


## Sample 

It is a random generated sample with $1024 \times 1024$ resolution. I added some white Guassian noise to simulate the random noise while imaging. Due to in lensless microscope the FOV is equal to the active area on the sensor chip which is nearly equal to the size of the sample. So the **FOV** here is $204.8\mu m \times 204.8\mu m$ .

<div align="center">
    <figure>
        <img src="./under_sampling_pic/microscopic_sample_no_grid.png" width="400">
    </figure>
</div>

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



🚩**3. Under-sampling with $1.6 \mu m$ pixel size**

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