---
title: 替换hexo默认的markdown生成器
date: 2018-01-24 15:59:35
tags:
- hexo
categories:
- hexo
---

<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>

### 前言

hexo官方介绍，支持 GitHub Flavored Markdown 的所有功能。但是生成的一些效果和GitHub风格的并不一样，最令我无法忍受的一点就是，在文本编辑器中插入换行，
生成的html中就加入一个&lt;br&gt;但是在页面上我并不期待他换行。

<!-- more -->
### 替换markdown生成插件

在hexo的插件列表搜索markdown，发现有三个markdown插件。
![tu](https://raw.githubusercontent.com/Gengry/blogImage/master/20180124/1.jpg)
这个插件让hexo使用Markdown-it作为渲染引擎，支持MarkDown，GFM，CommonMark风格。  
#### 卸载默认插件
```
	npm un hexo-renderer-marked --save
```

### 安装

```
	npm i hexo-renderer-markdown-it --save
```
### 简单配置

在hexo博客的配置文件中`_config.yml`添加如下代码
```
	# Markdown-it config
	## Docs: https://github.com/celsomiranda/hexo-renderer-markdown-it/wiki/
	# markdown: 'zero'
	# markdown: 'default'
	markdown: 'commonmark'
```

我使用的是commonmark。

### 高级配置

```
	# Markdown-it config
	## Docs: https://github.com/celsomiranda/hexo-renderer-markdown-it/wiki
	markdown:
	  render:
		html: true
		xhtmlOut: false
		breaks: true
		linkify: true
		typographer: true
		quotes: '“”‘’'
	  plugins:
		- markdown-it-abbr
		- markdown-it-footnote
		- markdown-it-ins
		- markdown-it-sub
		- markdown-it-sup
	  anchors:
		level: 2
		collisionSuffix: 'v'
		permalink: true
		permalinkClass: header-anchor
		permalinkSymbol: ¶
```

参考：
* [Hexo 插件指南](http://wdxtub.com/2015/12/06/hexo-plugins-guide/)