### HTML5 a标签download属性的存在跨域问题

- 跨域的资源打开一个的标签
- 非跨域的资源直接下载(我们所希望的)

```

<!DOCTYPE html>
<html>
<body>

<p>点击 W3School 的 logo 来下载该图片：<p>

<a href="./w3logo.gif" download="test.gif">
<img border="0" src="http://www.w3school.com.cn/i/w3school_logo_white.gif" alt="W3School">

</a>

<a href="http://www.w3school.com.cn/i/eg_tulip.jpg" download="test.jpg">
<img border="0" src="http://www.w3school.com.cn/i/eg_tulip.jpg" alt="W3School">

</a>


<iframe id="iframe" src="http://res.hualala.com" style="display:block;">
<a href="http://res.hualala.com/group3/M03/4D/25/wKgVw1r7tR6s7fv4AAAYHny1Ero670.jpg" download="test.jpg">
<img border="0" src="http://res.hualala.com/group3/M03/4D/25/wKgVw1r7tR6s7fv4AAAYHny1Ero670.jpg" alt="W3School">

</a>
</iframe>
<script>

    var a =document.createElement('a');
    a.href="http://res.hualala.com/group3/M03/4D/25/wKgVw1r7tR6s7fv4AAAYHny1Ero670.jpg";
    console.dir(window.frames['iframe'])
    a.download="tests.jpg"
    
    document.getElementById('iframe').contentWindow.document.body .appendChild(a);
    a.click()
</script>
</body>
</html>

```
