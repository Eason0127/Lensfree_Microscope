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


<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Under-sampling simulation</title>
  <script async src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

  <style>
    body {
      display: flex;
      margin: 0;
      font-family: Arial, sans-serif;
    }

    /* 侧边栏样式 */
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
      transition: width 0.3s ease;
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

    /* 侧边栏折叠状态 */
    #sidebar.collapsed {
      width: 0;
      padding: 0;
      overflow: hidden;
    }

    /* 内容区域样式 */
    #content {
      margin-left: 220px;
      padding: 20px;
      width: calc(100% - 220px);
      transition: margin-left 0.3s ease, width 0.3s ease;
    }

    /* 内容区域扩展样式（当侧边栏折叠时） */
    #content.expanded {
      margin-left: 20px;
      width: calc(100% - 20px);
    }

    /* 隐藏正文内容，只保留标题 */
    #content p,
    #content div:not(h1, h2, h3) {
      display: none;
    }

    /* 折叠/展开按钮 */
    #toggle-btn {
      position: fixed;
      left: 200px;
      top: 10px;
      background-color: #007bff;
      color: white;
      border: none;
      padding: 5px 10px;
      cursor: pointer;
      font-size: 18px;
      transition: left 0.3s ease;
    }

    /* 按钮位置变化 */
    #sidebar.collapsed + #toggle-btn {
      left: 10px;
    }

    #toggle-btn:hover {
      background-color: #0056b3;
    }
  </style>
</head>
<body>

<!-- 侧边栏 -->
<div id="sidebar">
  <h2>目录</h2>
  <ul id="toc"></ul>
</div>

<!-- 折叠/展开按钮 -->
<button id="toggle-btn" onclick="toggleSidebar()">☰</button>

<!-- 主要内容，只显示标题 -->
<div id="content">
  <h1>Under-sampling simulation</h1>

  <h2>Sample</h2>

  <h2>Shannon sampling criteria</h2>

  <h2>Simulation method</h2>

  <h2>Test</h2>

  <h2>Conclusion</h2>
</div>

<script>
  window.MathJax = {
    tex: {
      inlineMath: [['$', '$']],
      displayMath: [['\\[', '\\]'], ['$$', '$$']],
      processEscapes: true  
    },
    svg: { scale: 1.2 }
  };

  document.addEventListener("DOMContentLoaded", function() {
    MathJax.typesetPromise();
    
    // 自动生成目录
    let toc = document.getElementById("toc");
    let headers = document.querySelectorAll("#content h1, #content h2, #content h3"); 

    headers.forEach(header => {
      let id = header.id || header.textContent.trim().replace(/\s+/g, "-").toLowerCase();
      header.id = id;

      let li = document.createElement("li");
      li.innerHTML = `<a href="#${id}">${header.textContent}</a>`;
      toc.appendChild(li);
    });
  });

  // 侧边栏折叠功能
  function toggleSidebar() {
    let sidebar = document.getElementById("sidebar");
    let content = document.getElementById("content");
    let toggleBtn = document.getElementById("toggle-btn");

    if (sidebar.classList.contains("collapsed")) {
      sidebar.classList.remove("collapsed");
      content.classList.remove("expanded");
      toggleBtn.innerHTML = "☰"; // 恢复按钮图标
    } else {
      sidebar.classList.add("collapsed");
      content.classList.add("expanded");
      toggleBtn.innerHTML = "→"; // 更改按钮图标
    }
  }
</script>

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