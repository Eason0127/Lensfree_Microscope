<script type="text/x-mathjax-config;executed=true">
        window.MathJax.Hub.Config({
            showProcessingMessages: false, //关闭js加载过程信息
            messageStyle: "none", //不显示信息
            jax: ["input/TeX", "output/HTML-CSS"],
            tex2jax: {
                inlineMath: [["$", "$"], ["\\(", "\\)"]], //行内公式选择符
                displayMath: [["$$", "$$"], ["\\[", "\\]"]], //段内公式选择符
                skipTags: ["script", "noscript", "style", "textarea", "pre", "code", "a"] //避开某些标签
            },
            "HTML-CSS": {
                availableFonts: ["STIX", "TeX"], //可选字体
                showMathMenu: false //关闭右击菜单显示
            }
        });
        //下面第三个参数可以不写，默认对整个html内的latex进行翻译
        window.MathJax.Hub.Queue(["Typeset", MathJax.Hub,document.getElementsByClassName("ck-content")]);
</script>


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

I want to achieve the following effect:

1. 

## Test 1 with 5mm sample-sensor distance

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