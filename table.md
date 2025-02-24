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

<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
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

    // 自动生成目录
    document.addEventListener("DOMContentLoaded", function () {
      let toc = document.getElementById("toc");
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
  <ul id="toc"></ul>
</div>

</body>
</html>
