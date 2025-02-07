# 简介

## Google Reader 是什么

**Google阅读器**（Google Reader）曾是Google公司旗下一个基于网络的聚合器，能在线或者离线阅读[Atom](https://zh.wikipedia.org/wiki/Atom_(標準))和[RSS](https://zh.wikipedia.org/wiki/RSS)。英文版的Google Reader于2005年10月7日通过[Google实验室](https://zh.wikipedia.org/wiki/Google实验室)发布，2007年9月17日成为正式版。中文版的Google阅读器大约在2007年9月18日左右发布。

2013年3月13日Google宣布，作为第二个春季大扫除计划之一，Google阅读器因用户数量逐年下降，于2013年7月1日终止服务

## FreshRSS / Google Reader API  是什么

FreshRSS是一个自托管的RSS聚合器。它轻量级、易于使用、功能强大且可定制。

FreshRSS / Google Reader API 提供了与其后端交互的能力，使其对于开发者更灵活

!!! note
    Google从未公开发表其API文档，现有的API文档都是十几年前基于逆向而得出的结果

现在，Google Reader API与[Fresh RSS](https://freshrss.github.io/FreshRSS/en/developers/06_GoogleReader_API.html),[Tiny Tiny RSS](https://github.com/eric-pierce/freshapi),[miniflux](https://miniflux.app/docs/google_reader.html)等开源RSS服务相兼容，赋予更多RSS阅读器/中间件更大的交互可能，本文档编写目的即为方便开发者进行调用（如果有人愿意根据此文档开发SDK那就更好了！）

## FreshRSS / Google Reader API  不是什么

FreshRSS / Google Reader API 本身并不是RSS订阅的具体提供方，即其本身并不提供内容分发服务，其只是用于方便访问各种RSS后端的API，且具体的可访问要参考具体后端的实现（部分方法可能并未被所有后端/插件支持）

## 组成部分

本文档分为两部分

- 身份鉴权：获取SID/LSID/Auth，以及用于操作的T token
- API调用：利用上面的鉴权项，进行对阅读器的操作
