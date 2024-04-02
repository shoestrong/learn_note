# 下载PDF文件

解决在浏览器中下载pdf文件的问题

默认是打开pdf文件，而不是下载

::: code-group
```js:line-numbers [index.js]
const express = require('express')
const cors = require('cors')
const axios = require('axios')
const path = require('path')

const app = express()

app.use(cors())

app.get('/', async (req, res) => {
  res.sendFile(path.join(__dirname, '.', 'index.html'))
})

app.get('/downloadPdf', (req, res) => {
  if (!Object.keys(req.query).length) {
    res.status(400).send('Invalid request')
    return
  }
  const { url, filename } = req.query
  if (!url) {
    res.status(400).send('Invalid request, PDF link that needs to be converted')
    return
  }
  console.log(req.headers['content-disposition']);
  axios({
    method: 'get',
    url,
    responseType: 'stream',
  }).then(response => {
    const name = filename ? (filename.split('.').pop() === 'pdf' ? filename : filename + '.pdf') : response.request.path.split('/').pop()
    res.set("content-type", "application/pdf")
    // 如果name为空，会默认采用a链接中download属性中的文件名，eg: <a href="" download="a.pdf"> 文件名是a.pdf
    // download值可以带文件后缀，如果不带后缀默认会追加, eg: <a href="" download="a"> 文件名也是a.pdf
    // 如果download也没设置，默认名字是请求地址名称，eg: downloadPdf.pdf
    // filename 优先级，query中filename > url中名称（通过截取）> a标签中download名称 > 没有设置任何设置（默认获取的是downloadPdf.pdf）
    res.set('content-disposition', 'attachment;filename=' + name)
    response.data.pipe(res)
  })
})

app.listen(3000, () => {
  console.log('listen at http://localhost:3000');
})
```
```html:line-numbers [index.html]
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>下载PDF</title>
</head>
<body>
  <h1>
    <a href="https://www.npci.org.in/PDF/nach/live-members-e-mandates/Live-Banks-in-API-E-Mandate.pdf" download>
      直接打开PDF文件
    </a>
  </h1>
  <h1>
    <a href="http://localhost:3000/downloadPdf?url=https://www.npci.org.in/PDF/nach/live-members-e-mandates/Live-Banks-in-API-E-Mandate.pdf&filename=custom-filename-file.pdf" download>
      下载PDF文件
    </a>
  </h1>
</body>
</html>
```
:::