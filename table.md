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

<body>
<!--侧栏界面设计-->
<div id="mySidenav" class="sidenav">
  <a href="javascript:void(0)" class="closebtn" onclick="closeNav()">&times;</a>
  <a href="#">About</a>
  <a href="#">Services</a>
  <a href="#">Clients</a>
  <a href="#">Contact</a>
</div>
 
<div id="main">
  <h2>侧边栏实例 - 页面主体向右移动</h2>
  <p>点击以下菜单图标打开侧边栏，主体内容向右偏移。主体内容添加黑色透明背景</p>
  <span style="font-size:30px;cursor:pointer" onclick="openNav()">&#9776; open</span>
</div>
</body>
<script type="text/javascript">
/*打开侧栏，修改侧栏宽度，主体左跨度、背景透明度*/
    function openNav() {
        document.getElementById("mySidenav").style.width = "250px";
	document.getElementById("main").style.marginLeft = "250px";
    	document.body.style.backgroundColor = "rgba(0,0,0,0.4)";
    }
    /*关闭侧栏，恢复原始侧栏宽度，主体左跨度、背景透明度*/
    function closeNav() {
	document.getElementById("mySidenav").style.width = "0";
	document.getElementById("main").style.marginLeft= "0";
	document.body.style.backgroundColor = "white";
    }
</script>

@charset "utf-8";
/* CSS Document */
 
body {
    font-family: "Lato", sans-serif;
    transition: background-color .5s;
}
 
/*侧边栏选择器*/
.sidenav {
    height: 100%;
    width: 0; /*原始宽度*/
    position: fixed;
    /*z-index、top、left共同控制侧栏的悬浮（上方1，下方-1）*/
    z-index: 1;
    top: 0;
    left: 0;
    background-color: #111;
    overflow-x: hidden;
    transition: 0.5s; /*侧栏延迟0.5s显示*/
    padding-top: 60px;
}
 
/*侧边栏标签选择器*/
.sidenav a {
    padding: 8px 8px 8px 32px;
    text-decoration: none;
    font-size: 25px;
    color: #818181;
    display: block;
    transition: 0.3s; /*标签延迟0.3s显示*/
}
 
/*侧栏标签和关闭按钮光标的效果*/
.sidenav a:hover, .offcanvas a:focus{
    color: #f1f1f1;
}
 
/*侧栏和关闭按钮的位置选择器*/
.sidenav .closebtn {
    position: absolute;
    top: 0;
    right: 25px;
    font-size: 36px;
    margin-left: 50px;
}
 
/*主体内容*/
#main {
    transition: margin-left .5s;/*主体内容延迟0.5s整体y右移动*/
    padding: 16px;
}
 
/*当文档高度小于450px时，改变侧栏的padding属性和字体大小*/
@media screen and (max-height: 450px) {
  .sidenav {padding-top: 15px;}
  .sidenav a {font-size: 18px;}
}