---
layout: pagelt
title: 软件开发项目管理系统
tags: [byd]
---
### 个人简单调研
---

延续去年立下的flag，当我重新计划执行codereview的时候，想起去年注册的team@OSC却发现服务已经关闭，9个月之前就已经引导用户迁移至gitee企业版了。不过好在我已经找到了更好的替代：

Coding个人版和企业版: 
- [企业版](edv.coding.net)
- [个人版](dev.tencent.com)

顺便推荐大家试用下：
[Cloud Studio](https://coding.net/products/cloudstudio)

虽然Coding的后台腾讯财大气粗，作为后入者也格外慷慨（[查看收费](https://coding.net/pricing)），个人版同gitee差不多，但是提供了平台自带的持续集成功能（官网显示为测试性功能需手动开启，无语的是我的账号启用并关闭后居然就无法再次启用了），Coding平台自带的DevOps功能是一大亮点。企业版提供小于5人团队不限量的免费使用，功能和教程也都比较全，收费模式已经建立，短期来看应该也不会有大的调整。

对于小公司来说，自建源码仓库应该是个不错的选择，svn server比较常用的有[visualsvn](https://www.visualsvn.com/), 但是功能和git servers们比较起来就弱多了，git servers的选项也比较多,opensource Git Servers 开源产品举例: 
- Gitlabs（开发语言同github一样：ruby）
- [Gogs](https://gogs.io/)（开发语言：Go）
- [Gitea](https://gitea.io)（开发语言：Go）

比较早的gitlabs已经开始了商业化之旅，Gitea是在Gogs的基础上发展起来的，这两个目前都还在开源当中，而且对比gitlabs的官网，中文支持要好一些(当然喽，国人开发的)。哦补充下，这些像githab一样好用的git servers都不支持svn。/(ㄒoㄒ)/~~

但我们公司的情况，估计将全部将svn项目迁移至git不太现实，虽然理论上可以操作。

近期发现一个BSD开源项目Trac，同时支持svn、git以及其他源码管理，功能和coding有点相似，支持wiki,timeline,milestone,tickets以及强大的搜索功能，虽然对比coding在功能和界面上都相差甚远，但是它最大的优点就是[官方文档](https://trac.edgewall.org/)非常全面细致，功能简单且基础，支持插件开发。Trac这个项目好像是用python开发的。

参考链接：
- <https://docs.gitea.io/zh-cn/comparison/>
- <https://coding.net/products/vs-github>
- <https://trac.edgewall.org/>
