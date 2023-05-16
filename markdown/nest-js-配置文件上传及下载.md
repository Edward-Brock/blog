# nest-js-配置文件上传及下载

最近使用 Nest.js 做项目，需要用到文件上传及下载功能，关于 Nest.js 的上传、下载文件一直没有仔细研究过，经过将近两天的查阅资料和动手实践，整合相关 Nest.js 上传、下载文件的相关方法。

## 全局配置

`main.ts`

```typescript
async function bootstrap() {
  const app = await NestFactory.create(AppModule);

  // 配置 public 文件夹为静态目录，以达到可直接访问下面文件的目的
  const rootDir = join(__dirname, '..');
  app.use('/public', express.static(join(rootDir, 'public')));
  
  app.useGlobalPipes(new ValidationPipe());
  app.useGlobalFilters(new HttpExceptionFilter());
  app.useGlobalInterceptors(new TransformInterceptor());
  await app.listen(3000);
}
```

### 创建 upload 模块

`nest g resource upload --no-spec`

## 模块配置

`upload.module.ts`

在 Module 中添加全局配置，这种方式会将内容存储在 `public/upload/` 目录下，并根据代码中配置进行判断，如果文件夹中不包含分类文件夹则自动创建

```typescript
import { Module } from '@nestjs/common';
import { UploadService } from './upload.service';
import { UploadController } from './upload.controller';
import { MulterModule } from '@nestjs/platform-express';
import { diskStorage } from 'multer';
import { extname } from 'path';
import { MulterOptions } from '@nestjs/platform-express/multer/interfaces/multer-options.interface';
import { checkDirAndCreate } from '../../utils/checkDirAndCreate';
const image = ['gif', 'png', 'jpg', 'jpeg', 'bmp', 'webp'];
const video = ['mp4', 'webm'];
const audio = ['mp3', 'wav', 'ogg'];
const work = ['txt', 'rtf', 'pdf', 'xls', 'xlsx', 'doc', 'docx', 'ppt', 'pptx'];

// 配置文件上传
const multerOptions: MulterOptions = {
  // 配置文件的存储
  storage: diskStorage({
    // 存储地址
    // 配置文件上传后的文件夹路径
    destination: (req, file, cb) => {
      // 根据上传的文件类型将图片视频音频和其他类型文件分别存到对应英文文件夹
      const mimeType = file.mimetype.split('/')[1];
      let temp = 'other';
      image.filter(item => item === mimeType).length > 0
        ? (temp = 'image')
        : '';
      video.filter(item => item === mimeType).length > 0
        ? (temp = 'video')
        : '';
      audio.filter(item => item === mimeType).length > 0
        ? (temp = 'audio')
        : '';
      work.filter(item => item === mimeType).length > 0
        ? (temp = 'file')
        : '';

      const filePath = `./public/upload/${temp}/`;
      checkDirAndCreate(filePath); // 判断文件夹是否存在，不存在则自动生成
      return cb(null, `./${filePath}`);
    },
    // 存储名称
    filename: (req, file, callback) => {
      const suffix = extname(file.originalname); // 获取文件后缀
      const docName = new Date().getTime(); // 自定义文件名
      return callback(null, `${docName}${suffix}`);
    }
  }),
  // 过滤存储的文件
  fileFilter: (_req, file, callback) => {
    // multer 默认使用 latin1 编码来解析文件名, 而 latin1 编码不支持中文字符, 所以会出现中文名乱码的现象
    // 这里将文件名从 latin1 编码转换为 Buffer 对象, 再用 toString('utf8') 将 Buffer 对象转换为 utf8 编码的字符串
    // utf8 是一种支持多国语言的编码方式, 这样就可以保证文件名的中文字符不会被错误解析
    file.originalname = Buffer.from(file.originalname, 'latin1').toString(
      'utf8',
    );
    callback(null, true);
  },
  // 限制文件大小
  limits: {
    // 限制文件大小为 10 MB
    fileSize: 10 * 1024 * 1024, // 默认无限制
    // 限制文件名长度为 50 bytes
    fieldNameSize: 50, // 默认 100 bytes
  }
};

@Module({
  imports: [MulterModule.register(multerOptions),],
  controllers: [UploadController],
  providers: [UploadService]
})
export class UploadModule { }
```

`checkDirAndCreate.ts`

```typescript
// src/utils/checkDirAndCreate.ts
import * as fs from 'fs';

export const checkDirAndCreate = (filePath: string) => {
    const pathArr = filePath.split('/');
    let checkPath = '.';
    let item: string;
    for (item of pathArr) {
        checkPath += `/${item}`;
        if (!fs.existsSync(checkPath)) {
            fs.mkdirSync(checkPath);
        }
    }
};
```

## 上传单个文件

```typescript
import { Controller, Post, UseInterceptors ,UploadedFile} from '@nestjs/common';
import { FileInterceptor } from '@nestjs/platform-express';

@Post('upload')
@UseInterceptors(FileInterceptor('file'))
uploadFile(@UploadedFile() file) {
  console.log(file);
}
```

现在让我们测试下，看下收到的 file 是什么样的。可以使用 postman，或者其他工具，这里不介绍如何使用工具，如果需要可在评论区评论。如下：

```typescript
{
  fieldname: 'file',
  originalname: 'ceshi.png',
  encoding: '7bit',
  mimetype: 'image/png',
  buffer: <Buffer 89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52 00 00 07 80 00 00 04 38 08 06 00 00 00 e8 d3 c1 43 00 00 20 00 49 44 41 54 78 9c ec dd 77 9c 94 f5 bd ... 1874657 more bytes>,
  size: 1874707
}
```

可以看到我们获得的一个文件的详细信息，以及一个 buffer，我们可以通过收到的 buffer 进行存储，如下。

```typescript
import * as fs from 'fs';

fs.writeFileSync('./hah.jpg', file.buffer);
```

目前为止我们已经学会如何上传一个文件以及存储。

上面这种方式，每次都需要自已写保存文件的方式，可以通过配置，来定义上传。

## 上传多个文件

如下，与上面的不同之处有两个 `FilesInterceptor`，`UploadedFiles`

```typescript
import { Controller, Post, UseInterceptors, UploadedFile, UploadedFiles } from '@nestjs/common';
import { FileInterceptor, FilesInterceptor } from '@nestjs/platform-express';

@Post('upload')
@UseInterceptors(FilesInterceptor('file'))
uploadFile(@UploadedFiles() files) {
  console.log(files);
}
```

日志如下：

```typescript
[
  {
    fieldname: 'file',
    originalname: '1.png',
    encoding: '7bit',
    mimetype: 'image/png',
    buffer: <Buffer 89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52 00 00 07 80 00 00 04 38 08 06 00 00 00 e8 d3 c1 43 00 00 20 00 49 44 41 54 78 9c ec dd 7b ac 6d d9 55 ... 1770419 more bytes>,
    size: 1770469
  },
  {
    fieldname: 'file',
    originalname: '2.png',
    encoding: '7bit',
    mimetype: 'image/png',
    buffer: <Buffer 89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52 00 00 07 80 00 00 04 38 08 06 00 00 00 e8 d3 c1 43 00 00 20 00 49 44 41 54 78 9c ec dd 77 9c 94 f5 bd ... 1874657 more bytes>,
    size: 1874707
  }
]
```

## 自定义不同的字段名称键

```typescript
@Post('upload')
@UseInterceptors(FileFieldsInterceptor([
  { name: 'avatar', maxCount: 1 },
  { name: 'background', maxCount: 1 },
]))
uploadFile(@UploadedFiles() files) {
  console.log(files);
}
```

也可携带其他参数

```typescript
 @Post('uploads')
    @UseInterceptors(FileFieldsInterceptor([
        { name: 'avatar', maxCount: 1 },
        { name: 'background', maxCount: 1 },
        { name: 'avatar_name'},
        { name: 'background_name'}
    ]))
    async uploads(@UploadedFiles() files,@Body() body) {
        console.log(files,body)
    }
```

日志如下：

```typescript
[Object: null prototype] {
  avatar: [
    {
      fieldname: 'avatar',
      originalname: '应用预览图-1.png',
      encoding: '7bit',
      mimetype: 'image/png',
      buffer: <Buffer 89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52 00 00 07 80 00 00 04 38 08 06 00 00 00 e8 d3 c1 43 00 00 20 00 49 44 41 54 78 9c ec dd 7b ac 6d d9 55 ... 1770419 more bytes>,
      size: 1770469
    }
  ],
  background: [
    {
      fieldname: 'background',
      originalname: '应用预览图-3.png',
      encoding: '7bit',
      mimetype: 'image/png',
      buffer: <Buffer 89 50 4e 47 0d 0a 1a 0a 00 00 00 0d 49 48 44 52 00 00 07 80 00 00 04 38 08 06 00 00 00 e8 d3 c1 43 00 00 20 00 49 44 41 54 78 9c ec dd 77 9c 94 f5 bd ... 1838100 more bytes>,
      size: 1838150
    }
  ]
} [Object: null prototype] {
  avatar_name: 'ceshi',
  background_name: 'ceshi'
}
```

## 上传文件使用任意的字段名称

```typescript
  @Post('upload5')
  @UseInterceptors(AnyFilesInterceptor())
  uploadFileAny(@UploadedFiles() files) {
    console.log(files);
  }
```

## 参照文章

- [nest文件上传](https://zhuanlan.zhihu.com/p/158026491)
- [Nest.js 文件上传之动态自定义文件保存路径、文件名称、文件过滤](https://juejin.cn/post/6844903934973198344)
- [Nest.js 文件上传及自定义文件名保存](https://juejin.cn/post/6844903920171483144)
