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


<!DOCTYPE html>
<html>
<head>
  <style>
    /* 侧边栏样式 */
    .sidebar {
      position: fixed;
      left: 0;
      top: 0;
      height: 100%;
      width: 260px;
      background: #f5f5f5;
      padding: 20px;
      box-shadow: 2px 0 5px rgba(0,0,0,0.1);
      overflow-y: auto;
      transition: transform 0.3s ease;
    }

    /* 折叠按钮样式 */
    .toggle-btn {
      position: fixed;
      left: 270px;
      top: 20px;
      background: #666;
      color: white;
      border: none;
      padding: 10px;
      cursor: pointer;
      border-radius: 0 5px 5px 0;
    }

    /* 内容区域 */
    .content {
      margin-left: 300px;
      padding: 20px;
      max-width: 1200px;
    }

    /* 目录样式 */
    .toc {
      list-style: none;
      padding: 0;
    }
    .toc li {
      margin: 8px 0;
    }
    .toc a {
      color: #333;
      text-decoration: none;
      transition: color 0.3s;
    }
    .toc a:hover {
      color: #007bff;
    }
  </style>
</head>
<body>
  <!-- 侧边栏 -->
  <div class="sidebar" id="sidebar">
    <ul class="toc" id="toc"></ul>
  </div>
  
  <!-- 折叠按钮 -->
  <button class="toggle-btn" id="toggleBtn">◀</button>

  <!-- 内容区域 -->
  <div class="content">
    <!-- 保持原有内容结构，只需给每个章节标题添加ID -->
    <h2 id="sample">Sample</h2>
    <p>It is a random generated sample...</p>
    
    <h2 id="shannon">Shannon sampling criteria</h2>
    <p>If we want to recover...</p>

    <h2 id="simulation">Simulation method</h2>
    <p>I wanted to test...</p>

    <h2 id="test">Test</h2>
    <p>1. Ideal case...</p>

    <h2 id="conclusion">Conclusion</h2>
    <p>I think the RMS error...</p>
  </div>

  <script>
    // 自动生成目录
    function generateTOC() {
      const headings = document.querySelectorAll('.content h2');
      const toc = document.getElementById('toc');
      
      headings.forEach(heading => {
        const li = document.createElement('li');
        const link = document.createElement('a');
        link.textContent = heading.textContent;
        link.href = `#${heading.id}`;
        link.onclick = (e) => {
          e.preventDefault();
          document.querySelector(heading.getAttribute('id')).scrollIntoView({
            behavior: 'smooth'
          });
        };
        li.appendChild(link);
        toc.appendChild(li);
      });
    }

    // 折叠功能
    let isCollapsed = false;
    document.getElementById('toggleBtn').addEventListener('click', () => {
      const sidebar = document.getElementById('sidebar');
      isCollapsed = !isCollapsed;
      sidebar.style.transform = isCollapsed ? 'translateX(-100%)' : 'translateX(0)';
      document.getElementById('toggleBtn').style.left = isCollapsed ? '0' : '270px';
      document.getElementById('toggleBtn').innerHTML = isCollapsed ? '▶' : '◀';
    });

    // 初始化
    window.addEventListener('DOMContentLoaded', () => {
      generateTOC();
      
      // 等待MathJax渲染完成
      MathJax.typesetPromise().then(() => {
        generateTOC(); // 重新生成目录确保定位准确
      });
    });
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