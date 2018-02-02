---
title: JDK动态代理引发的Spring事物不能回滚原因分析
date: 2018-02-02 09:59:52
tags:
categories:
---

12.架构师不得不知道的Spring事物不能回滚的深层次原因
https://pan.baidu.com/mbox/streampage?from_uk=684610632&msg_id=8376247896642219120&fs_id=476303219573352&to=763122753997696274&type=2&name=12.%E6%9E%B6%E6%9E%84%E5%B8%88%E4%B8%8D%E5%BE%97%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84Spring%E4%BA%8B%E7%89%A9%E4%B8%8D%E8%83%BD%E5%9B%9E%E6%BB%9A%E7%9A%84%E6%B7%B1%E5%B1%82%E6%AC%A1%E5%8E%9F%E5%9B%A0.mp4&path=%2FB12%E3%80%81%E9%9D%A2%E8%AF%95%E4%B8%93%E5%B1%9E%2F12.%E6%9E%B6%E6%9E%84%E5%B8%88%E4%B8%8D%E5%BE%97%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84Spring%E4%BA%8B%E7%89%A9%E4%B8%8D%E8%83%BD%E5%9B%9E%E6%BB%9A%E7%9A%84%E6%B7%B1%E5%B1%82%E6%AC%A1%E5%8E%9F%E5%9B%A0%2F12.%E6%9E%B6%E6%9E%84%E5%B8%88%E4%B8%8D%E5%BE%97%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84Spring%E4%BA%8B%E7%89%A9%E4%B8%8D%E8%83%BD%E5%9B%9E%E6%BB%9A%E7%9A%84%E6%B7%B1%E5%B1%82%E6%AC%A1%E5%8E%9F%E5%9B%A0.mp4&md5=53444f5643355dab35b28dabd3e0b0f1
https://www.cnblogs.com/hanxue53/p/5280099.html
https://www.cnblogs.com/doudouxiaoye/p/5789282.html
https://www.cnblogs.com/puyangsky/p/6661638.html
<blockquote class="blockquote-center">今天最好的表现，是明天最低的要求。</blockquote>