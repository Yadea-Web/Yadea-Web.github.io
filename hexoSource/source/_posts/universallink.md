---
title: iOS Universal Link配置详解
date: 2019-12-17 17:45:27
tags: iOS
category: iOS
---

iOS13之后，H5打开App时使用scheme的方式会在打开App后跳转到下载链接页面。参考知乎的实现方式，我们决定升级成Universal Link的方式，对用户更加友好。

<!-- more -->

以下为具体步骤：

1. 在开发者中心证书配置页找到Identifiers，然后点击项目的BundleID进入详情页，选中Associated Domains，如截图所示。
<img src="pic1.jpg" width="100%"/>
2. 参考知乎的apple-app-site-association文件([https://oia.zhihu.com/apple-app-site-association](https://oia.zhihu.com/apple-app-site-association))，写一份放到H5目录下（文件修改参考截图，多余的删掉即可）
	1. 文件不要带后缀
	2. 必须为https
	3. 浏览器访问https://`你的下载页地址`/apple-app-site-association时会返回apple-app-site-association文件的内容（不是下载，是会把文件内容展示出来，这两个有区别）
<img src="pic2.jpg" width="100%"/>
<img src="pic4.jpg" width="100%"/>
4. 在Xcode中配置applink，参考截图
<img src="pic3.jpg" width="100%"/>
5. 在AppDelegate.m文件中配置以下代码：

```
- (BOOL)application:(UIApplication *)application continueUserActivity:(NSUserActivity *)userActivity restorationHandler:(void (^)(NSArray<id<UIUserActivityRestoring>> * _Nullable))restorationHandler {
    if (![userActivity.activityType isEqualToString:NSUserActivityTypeBrowsingWeb]) {
        return YES;
    }

    NSURL *jumpUrl = userActivity.webpageURL; // 这个URL就是你跳转的那个URL，下载页地址，想要传参数可以直接在URL后面拼接
    // TODO：在这里进行跳转具体页面的操作
    return YES;
}
```

6. 注意事项：
	1. H5的域名和下载页的域名不能是同一个域名
	2. 浏览器访问和Xcode中配置的都是下载页的域名
	3. H5分享页不要求https，但下载页必须是https

	
### 在H5中的实现

1. 在分享页面加一个在App中打开按钮
2. 点击跳转下载页面
3. 此时若已安装App，则自动打开App。没有安装App会跳转到下载页
4. 点击下载页的立即下载按钮，重定向到App Store即可

