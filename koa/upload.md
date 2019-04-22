# koa-body中间件实现图片上传接口

```javascript
const Koa = require('koa')
const Router = require('koa-router')
const koaBody = require('koa-body')
const fs = require('fs')
const path = require('path')
const app = new Koa()
const router = new Router();

app.use(koaBody({
    multipart: true,
    formidable: {
        maxFileSize: 200*1024*1024    // 设置上传文件大小最大限制，默认2M
    }
}));

router.post('/upload', async (ctx, next) => {

    const imgPath = 'img/'
    // 上传多个文件
    let files = ctx.request.files.file; // 获取上传文件
    if(!Array.isArray(files)){
        files = [files]
    }
    for (let file of files) {
      // 创建可读流
      const reader = fs.createReadStream(file.path);
      // 获取上传文件扩展名
      let filePath = path.join(__dirname, imgPath) + `/${file.name}`;
      // 创建可写流
      const upStream = fs.createWriteStream(filePath);
      // 可读流通过管道写入可写流
      reader.pipe(upStream);
    }
   return ctx.body = "上传成功！";
  });


app.use(router.routes())
    .use(router.allowedMethods());

app.listen(3000, () => {
    console.log('starting at port 3000');
});
```