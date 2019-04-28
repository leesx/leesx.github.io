### HTML5 a标签download属性的存在跨域问题

- 跨域的资源打开一个的标签
- 非跨域的资源直接下载(我们所希望的)

```

<!DOCTYPE html>
<html>
 <head></head>
 <body> 
  <p>下载该图片：</p>
  <p> </p>
  <h2>非跨域下的图片资源</h2> 
  <div> 
   <a href="./w3logo.gif" download="test.gif"> <img border="0" src="http://www.w3school.com.cn/i/w3school_logo_white.gif" alt="W3School" /> </a> 
  </div> 
  <h2>跨域下的图片资源</h2> 
  <div> 
   <a href="http://www.w3school.com.cn/i/eg_tulip.jpg" download="test.jpg"> <img border="0" src="http://www.w3school.com.cn/i/eg_tulip.jpg" alt="W3School" /> </a> 
  </div> 
  <iframe id="iframe" src="http://res.c.com" style="display:block;"> &lt;a href=&quot;http://res.leesx.com/group3/M03/4D/25/wKgVw1r7tR6s7fv4AAAYHny1Ero670.jpg&quot; download=&quot;test.jpg&quot;&gt; &lt;img border=&quot;0&quot; src=&quot;http://res.leesx.com/group3/M03/4D/25/wKgVw1r7tR6s7fv4AAAYHny1Ero670.jpg&quot; alt=&quot;W3School&quot;&gt; &lt;/a&gt; </iframe> 
  <script>

    var a =document.createElement('a');
    a.href="http://res.leesx.com/group3/M03/4D/25/wKgVw1r7tR6s7fv4AAAYHny1Ero670.jpg";
    console.dir(window.frames['iframe'])
    a.download="tests.jpg"
    
    document.getElementById('iframe').contentWindow.document.body .appendChild(a);
    a.click()
</script>   
 </body>
</html>
```
## 有效的方案
- server （nodejs koa2）

```
// 下载静态资源服务器资源
    router.get('/download', async (ctx) => {
        const { request } = ctx
        const { fileurl, filename } = request.query

        if (!fileurl) {
            throw new Error('fileurl is a required parameter')
        }

        // const url = combine(getServiceURL(service), method, ctx.search)
        // const options = await getOption(ctx)
        const { headers, body } = await ctx.fetch(fileurl, {})
        const refilename = filename || fileurl.split('/').pop();
        const DISPOSITION = `attachment; filename="${encodeURI(refilename)}"`;
        ctx.set(HTTP_HEADER.CONTENT_DISPOSITION, DISPOSITION)
        ctx.set('Content-Type', headers.get('content-type'))
        ctx.set('Content-Transfer-Encoding', 'binary')
        ctx.set('Cache-Control', 'no-cache')
        ctx.set('Expires', '0')
        ctx.set('Pragma', 'no-cache')
        ctx.body = body
    })
```

- 前端

> 必须通过 a 的href属性设置，不能通过ajax异步请求数据，否则无法触发浏览器的自动下载机制

```
<a href={`/download?fileurl=${record.fileUri}&filename=${record.fileName}.${record.suffixName}`}>下载测试</a>
```
