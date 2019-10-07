---
layout: default
title: 创建一个模块
permalink: /modules/creating-a-module/
redirect_from:
  - /creating_a_module.html
  - /modules/creating_a_module.html
sitemap:
    priority: 0.7
    lastmod: 2015-12-05T18:40:00-00:00
---

# <i class="fa fa-cube"></i> 创建一个模块

JHipster模块是一个Yeoman生成器，由特定的JHipster子生成器[组成](http://yeoman.io/authoring/composability.html)，以继承JHipster的某些常用功能。JHipster模块还可以注册自身以充当JHipster生成器的钩子。

JHipster模块在[JHipster市场]({{ site.url }}/modules/marketplace/)上列出。

这允许创建可以访问JHipster变量和函数，并像标准JHipster子生成器一样工作的第三方生成器。

钩子机制在应用程序生成和实体生成之前和之后调用第三方生成器。

## 示例

[JHipster Fortune module](https://github.com/jdubois/generator-jhipster-fortune) 在JHipster生成的应用程序中生成“fortune cookie”页面。

我们的示例模块展示了如何使用JHipster的变量和函数来创建自己的生成器。

或者，您可以使用[JHipster模块生成器](https://github.com/jhipster/generator-jhipster-module)来帮助您初始化模块。

## JHipster模块基本规则

JHipster模块：

- 是一个NPM软件包，并且是一个Yeoman生成器。
- 遵循[http://yeoman.io/generators/](http://yeoman.io/generators/)上列出的Yeoman扩展规则，并且可以使用"yo"命令进行安装，使用和更新。不能以 "generator-"为前缀，而是以"generator-jhipster-"为前缀，并且必须具有两个关键字"yeoman-generator"和"jhipster-module"，而不仅仅是"yeoman-generator"关键字。
- 注册为钩子的JHipster模块，不应在被挂载时调用`process.exit`。

## 导入generator-jhipster

JHipster模块必须导入generator-jhipster：

```
    const util = require('util');
    const BaseGenerator = require('generator-jhipster/generators/generator-base');
    const jhipsterConstants = require('generator-jhipster/generators/generator-constants');

    const JhipsterGenerator = generator.extend({});
    util.inherits(JhipsterGenerator, BaseGenerator);

    module.exports = JhipsterGenerator.extend({

        // all your yeoman code here

    });
```

然后，您可以直接访问JHipster的变量和函数。

## 钩子

JHipster将在其某些任务之前和之后调用某些钩子，下面列出了当前可用和计划中的任务。

- 实体创建后钩子
- 实体创建前钩子（已计划）
- 应用创建前钩子（已计划）
- 应用创建后钩子（已计划）

[JHipster模块生成器](https://github.com/jhipster/generator-jhipster-module)现在可以选择生成这个。当最终用户运行其主生成器时，JHipster模块可以注册为挂钩。您需要从主（应用）生成器中调用`registerModule`方法来注册为钩子，您需要在方法中传递以下参数，如下所示

```javascript
this.registerModule(npmPackageName, hookFor, hookType[, callbackSubGenerator[, description]])
```

- `npmPackageName` 生成器的npm软件包名称。 例如：`jhipster-generator-fortune`
- `hookFor` 钩子应从上面注册到哪个Jhipster钩子( 值必须`entity`或`app`)
- `hookType` 在生成器阶段将其挂载的阶段 (值必须`pre`或`post`)
- `callbackSubGenerator` [可选]要调用的子生成器，如果未指定，则将调用模块的主（应用）生成器，例如： `bar`或`foo`生成器
- `description` [可选]生成器的描述，如果未给出，我们将根据给定的npm名称生成一个默认值

## 可用的变量和方法

### 配置中的变量：

您必须使用此方法：

您可以在`.yo-rc.json`中访问配置：

```
    this.jhipsterAppConfig = this.getAllJhipsterConfig();
    this.baseName = this.jhipsterAppConfig.baseName;
    this.packageName = this.jhipsterAppConfig.packageName;
    this.clientFramework = this.jhipsterAppConfig.clientFramework;
```

### 全局变量：

您可以在[生成器常量](https://github.com/jhipster/generator-jhipster/blob/master/generators/generator-constants.js)中使用常量：

```
    const javaDir = `${jhipsterConstants.SERVER_MAIN_SRC_DIR + this.packageFolder}/`;
    const resourceDir = jhipsterConstants.SERVER_MAIN_RES_DIR;
    const webappDir = jhipsterConstants.CLIENT_MAIN_SRC_DIR;
```

### 方法:

您可以使用[generator-base](https://github.com/jhipster/generator-jhipster/blob/master/generators/generator-base.js)中所有的方法：

```
    this.angularAppName = this.getAngularAppName(); // get the Angular application name.
    this.printJHipsterLogo(); // to print the JHipster logo
```

**注意**：`generator-base.js`中的函数和`generator-constants.js`中的变量是公共API的一部分，因此将遵循semver versioning。但是其他文件（例如`generator-base-private.js`，`utils.js`等）将不会遵循semver versioning，并且可能会在次要版本中破坏方法签名。

## 向JHipster市场注册模块

要使模块在[JHipster市场]({{ site.url }}/modules/marketplace/)上可用，您需要确保在发布的`package.json`中有2个关键字：`yeoman-generator` 和`jhipster-module`。
如果您在市场中找到不是JHipster模块的任何条目，则可以通过对[jhipster/jhipster.github.io项目](https://github.com/jhipster/jhipster.github.io)进行Pull Request，将其添加到[modules-config.json文件](https://github.com/jhipster/jhipster.github.io/blob/master/modules/marketplace/data/modules-config.json)的`blacklistedModules`部分中，从而将其列入黑名单。

如果JHipster团队对其进行验证，则您的模块将变为“已验证”。

将模块发布到NPM后，您的模块将在我们的市场上可用。