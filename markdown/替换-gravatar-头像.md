# 替换 Gravatar 头像

之前重新配置 WordPress 后曾经替换过 Gravatar 的头像源，但是没有想到自己用的这个 WordPress 官方主题年份已经这么旧了，竟然还有更新，导致我写入 `functions.php` 内的代码被覆盖掉了，我之前的头像替换就丢失了。

因为之前有搜索过关于头像的插件但都没有找到简单、易使用的，有些插件不是太重、就是太旧，夹杂的东西很多都是用不上的，有的也跟我想要的纯粹的替换头像不太一样，所以就从网上搜索，自己再次替换 `functions.php` 这次并作一次记录，以防后续文件覆盖后丢失。

打开 `functions.php` 文件，并在文件内加入如下代码：

```php
/**
 * WordPress 头像
 */
function theme_get_avatar( $avatar ) {
 $avatar = preg_replace("/\/\/(www|\d|secure|cn).gravatar.com\/avatar\//", "//cdn.v2ex.com/gravatar/", $avatar);
 return $avatar;
}

add_filter('get_avatar', 'theme_get_avatar');
```

这次使用的是 V2EX 的 CDN 服务，针对国内和国外线路都有优化，而且还支持ssl访问。

同时从网上搜索了几个支持 Gravatar CDN 的网址：

- V2EX：[https://cdn.v2ex.com/gravatar/](https://cdn.v2ex.com/gravatar/)
- 极客族：[https://sdn.geekzu.org/avatar/](https://sdn.geekzu.org/avatar/)
- Loli：[https://gravatar.loli.net/avatar/](https://gravatar.loli.net/avatar/)
