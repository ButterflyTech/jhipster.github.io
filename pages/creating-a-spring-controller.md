---
layout: default
title: 创建一个控制器
permalink: /creating-a-spring-controller/
sitemap:
    priority: 0.7
    lastmod: 2019-02-01T00:00:00-00:00
---

# <i class="fa fa-bolt"></i> 创建一个Spring控制器

## 介绍

_注意：此子生成器比创建完整CRUD实体的[]实体子生成器]({{ site.url }}/creating-an-entity/)简单得多_

该子生成器生成一个Spring MVC REST Controller。它还能够创建一些简单的REST方法。

为了生成名为“Foo”的Spring MVC REST控制器，只需键入：

`jhipster spring-controller Foo`

子生成器将询问您要生成哪种方法：只需提供需要使用的方法名称和HTTP请求方法，就会生成一个简单的方法。

## 我们可以使用Swagger来自动生成这个Spring MVC REST Controller API文档吗？

没错! 已经实现了！在`dev`模式下，只需使用`Administration > API`菜单即可访问Swagger UI并开始使用生成的控制器。

## 我们可以在Spring MVC REST控制器上增加安全性吗？

可以! 只需在您的类或方法上添加Spring Security的`@Secured`注解，然后使用提供的`AuthoritiesConstants`类即可限制对特定用户权限的访问。

## 我们可以从微服务架构的Gateway开发服务上代理到它吗？

可以! 将服务名添加到`webpack/webpack.dev.js`中的代理配置中
```javascript

module.exports = (options) => webpackMerge(commonConfig({ env: ENV }), {
    devtool: 'eval-source-map',
    devServer: {
        contentBase: './target/www',
        proxy: [{
            context: [
                '/<servicename>',
                /* jhipster-needle-add-entity-to-webpack - JHipster will add entity api paths here */
                ....
```
