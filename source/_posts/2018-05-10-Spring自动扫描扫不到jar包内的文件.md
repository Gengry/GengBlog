---
title: Spring自动扫描扫不到jar包内的文件
date: 2018-05-10 10:45:03
tags:
categories:
---

公司内规定的开发模式，所有功能已模块的形式发布，classes目录内没有class文件，每个功能都是一个jar包，放在lib目录内。
有需求需要在原有功能给当地客户定义功能，考虑不侵入原有代码。将新开发的功能单独打一个jar包。
我使用的是 jar cvf test.jar com/xxx/xxx/xxx.class打包，但是发现打的包放到服务器上报的是404说明Spring mvc没有识别到Controller。
使用 jar tf test.jar 可以看到

<!--more-->

```
META-INF/MANIFEST.MF  
com/xxx/xxx/xxx.class  
```
这样spring是扫描不到的
我将test.jar解压，重新执行
```
cd test
jar cvf test.jar *
```
这样重新生成的目录结构就是
```
META-INF/MANIFEST.MF
com/
com/xxx
com/xxx/xxx/
com/xxx/xxx/xxx.class 
```

如果是使用eclipse导出勾选`Add directory entries`即可。

<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>