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
    /* 保持原有样式不变 */
    .sidebar { /* 原有样式 */ }
    .toggle-btn { /* 原有样式 */ }
    .content { /* 原有样式 */ }
    /* 新增标题样式 */
    .content h2 {
      cursor: pointer;
      position: relative;
      padding-left: 20px;
    }
    .content h2::before {
      content: "#";
      position: absolute;
      left: 0;
      color: #007bff;
      opacity: 0;
      transition: opacity 0.3s;
    }
    .content h2:hover::before {
      opacity: 1;
    }
  </style>
</head>
<body>
  <div class="sidebar" id="sidebar">
    <ul class="toc" id="toc"></ul>
  </div>

  <button class="toggle-btn" id="toggleBtn">◀</button>

  <!-- 原始内容保持完全不变 -->
  <div class="content">
    <h2>Sample</h2>
    <p>It is a random generated sample...</p>
    
    <h2>Shannon sampling criteria</h2>
    <p>If we want to recover...</p>

    <h2>Simulation method</h2>
    <p>I wanted to test...</p>

    <h2>Test</h2>
    <p>1. Ideal case...</p>

    <h2>Conclusion</h2>
    <p>I think the RMS error...</p>
  </div>

  <script>
    // 自动生成ID并创建目录
    function generateTOC() {
      const headings = document.querySelectorAll('.content h2');
      const toc = document.getElementById('toc');
      let idCounter = 1;

      headings.forEach(heading => {
        // 自动生成唯一ID
        if (!heading.id) {
          const baseID = heading.textContent
            .toLowerCase()
            .replace(/[^a-z0-9]+/g, '-')
            .replace(/(^-|-$)/g, '');
          heading.id = `${baseID}-${idCounter++}`;
        }

        // 创建目录项
        const li = document.createElement('li');
        const link = document.createElement('a');
        link.textContent = heading.textContent;
        link.href = `#${heading.id}`;
        
        // 增强的滚动逻辑
        link.onclick = (e) => {
          e.preventDefault();
          const target = document.getElementById(heading.id);
          const headerOffset = 80; // 根据实际布局调整
          const elementPosition = target.getBoundingClientRect().top;
          const offsetPosition = elementPosition + window.pageYOffset - headerOffset;

          window.scrollTo({
            top: offsetPosition,
            behavior: 'smooth'
          });

          // 更新URL hash
          history.pushState(null, null, `#${heading.id}`);
        };

        li.appendChild(link);
        toc.appendChild(li);
      });
    }

    // 增强的折叠功能
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
      // 等待MathJax渲染完成
      MathJax.typesetPromise().then(() => {
        generateTOC();
        
        // 处理页面加载时的hash定位
        if (window.location.hash) {
          const target = document.querySelector(window.location.hash);
          if (target) {
            setTimeout(() => {  // 等待布局稳定
              target.scrollIntoView({ behavior: 'smooth' });
            }, 300);
          }
        }
      });
    });

    // 窗口大小变化时保持定位准确
    window.addEventListener('resize', () => {
      if (window.location.hash) {
        const target = document.querySelector(window.location.hash);
        target && target.scrollIntoView();
      }
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