# WordPress 文章转为 Markdown 格式

因为 WordPress 导出的文章是 xml 格式的文件，如果想转到其他博客平台的话非常不方便，所以想将 WordPress 的文章转换成 Markdown 格式的文件。

上网搜了一下有很多这种功能的工具，我用的是 [Blogger to Markdown](https://github.com/palaniraja/blog2md) 这个工具。

这个工具使用很简单，如同自述文件里写的一样：

- 下载这个项目压缩包并解压缩
- `cd` 到该目录下
- 运行 `npm install` 安装依赖
- 运行 `node index.js <arg>`

因为我是要从 WordPress 导出为 Markdown 格式的文件，所以运行：

```js
node index.js w your-wordpress-backup-export.xml out
```

稍等一会你的所有文章就都统一在 `out` 文件夹中生成了。
