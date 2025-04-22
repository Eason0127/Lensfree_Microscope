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

<span style="color:red;">Question 3: How to select an illumniation?</span>


<span style="color:red;">Question 4: Twin image?</span>

"This additional boundary information regarding the sample is enough to enable the elimination of the twin-image artifact." --- Orzcan


