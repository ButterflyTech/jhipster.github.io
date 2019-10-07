---
layout: default
title: 创建蓝图
permalink: /modules/creating-a-blueprint/
redirect_from:
  - /creating_a_blueprint.html
  - /modules/creating_a_blueprint.html
sitemap:
    priority: 0.7
    lastmod: 2015-12-05T18:40:00-00:00
---

# <i class="fa fa-cube"></i> 创建蓝图

JHipster蓝图是一个Yeoman生成器，它由特定的JHipster子生成器[组成](http://yeoman.io/authoring/composability.html)，以扩展该子生成器的功能。该蓝图可以覆盖子生成器的任何已定义的getter，并提供其自己的模板和功能。

JHipster蓝图在[JHipster市场]({{ site.url }}/modules/marketplace/)上带有`jhipster-blueprint`标签。

这允许创建可以覆盖JHipster特定部分（例如仅客户端模板）的第三方蓝图。

## 使用蓝图

要使用蓝图，请运行以下命令

```bash
jhipster --blueprint <blueprint name>
```

## 示例

[JHipster Kotlin](https://github.com/jhipster/jhipster-kotlin)蓝图用等效的Kotlin代码替换了大多数服务器端Java代码。

这是我们的官方蓝图，展示了如何创建自己的蓝图。

[JHipster示例蓝图](https://github.com/hipster-labs/generator-jhipster-sample-blueprint)展示了如何覆盖客户端子生成器。

或者，您可以使用[JHipster蓝图生成器](https://github.com/jhipster/generator-jhipster-blueprint)来帮助您初始化蓝图。

要使用JHipster蓝图生成器，请运行以下命令

```bash
npm install -g generator-jhipster-blueprint

mkdir my-blueprint && cd my-blueprint

yo jhipster-blueprint
```

在回答问题时，选择您要覆盖的子生成器。

## JHipster蓝图的  基本规则

JHipster蓝图：

- 是NPM软件包，并且是Yeoman生成器。
- - 遵循[http://yeoman.io/generators/](http://yeoman.io/generators/)上列出的Yeoman扩展规则，并且可以使用"yo"命令进行安装，使用和更新。不能以 "generator-"为前缀，而是以"generator-jhipster-"为前缀，并且必须具有两个关键字"yeoman-generator"和"jhipster-module"，而不仅仅是"yeoman-generator"关键字。
- 蓝图只能扩展以下子生成器（在generators文件夹下）
    - common
    - client
    - server
    - entity
    - entity-client
    - entity-server
    - entity-i18n
    - languages
    - spring-controller
    - spring-service

## 导入generator-jhipster

JHipster蓝图必须具有generator-jhipster作为依赖项，并且应该导入适当的子生成器覆盖。

```javascript
    const chalk = require('chalk');
    const ClientGenerator = require('generator-jhipster/generators/client');
    ...

    module.exports = class extends ClientGenerator {
        constructor(args, opts) {
            super(args, Object.assign({ fromBlueprint: true }, opts)); // fromBlueprint variable is important

            const jhContext = this.jhipsterContext = this.options.jhipsterContext;

            if (!jhContext) {
                this.error(`This is a JHipster blueprint and should be used only like ${chalk.yellow('jhipster --blueprint helloworld')}`);
            }

            this.configOptions = jhContext.configOptions || {};
            // This sets up options for this sub generator and is being reused from JHipster
            jhContext.setupClientOptions(this, jhContext);
        }

        get initializing() {
            // Here we are not overriding this phase and hence its being handled by JHipster
            return super._initializing();
        }

        // other phases of the sub generator
    }
```

Any method beginning with `_` can be reused from the superclass that is being extended, for example `ClientGenerator` in the example above.
任何以`_`开头的方法都可以从被扩展的超类中重用，例如上例中的`ClientGenerator`。

每个JHipster子生成器都由多个yeoman阶段组成，每个阶段都是一个getter，例如`get initializing`。蓝图可以自定义其一个或多个阶段来覆盖子生成器。

有多种方法可以从JHipster定制一个阶段。

1) 让JHipster处理一个阶段，蓝图不会覆盖任何内容。

```javascript
    get initializing() {
        return super._initializing();
    }
```

2) 覆盖整个阶段，这是蓝图控制阶段的时候。

```javascript
    get initializing() {
        return {
            myCustomInitPhaseStep() {
                // Do all your stuff here
            },
            myAnotherCustomInitPhaseStep(){
                // Do all your stuff here
            }
        };
    }
```

3) 部分覆盖阶段，这是蓝图从JHipster获得阶段并对其进行自定义的时候。

```javascript
    get initializing() {
        const phaseFromJHipster = super._initializing();
        const myCustomPhaseSteps = {
            displayLogo() {
                // override the displayLogo method from the _initializing phase of JHipster
            },
            myCustomInitPhaseStep() {
                // Do all your stuff here
            },
        }
        return Object.assign(phaseFromJHipster, myCustomPhaseSteps);
    }
```

您还可以直接从蓝图访问JHipster的变量和函数。

## 可用的变量和方法

### 配置中的变量：

您可以访问`.yo-rc.json`中的配置，该配置将同时包含JHipster配置和您的蓝图配置。

### 全局变量：

您可以在[生成器常量](https://github.com/jhipster/generator-jhipster/blob/master/generators/generator-constants.js)中使用常量：

```javascript
    const javaDir = `${jhipsterConstants.SERVER_MAIN_SRC_DIR + this.packageFolder}/`;
    const resourceDir = jhipsterConstants.SERVER_MAIN_RES_DIR;
    const webappDir = jhipsterConstants.CLIENT_MAIN_SRC_DIR;
```

### 方法:

您可以使用在[generator-base](https://github.com/jhipster/generator-jhipster/blob/master/generators/generator-base.js)中所有功能：

```javascript
    this.angularAppName = this.getAngularAppName(); // get the Angular application name.
    this.printJHipsterLogo(); // to print the JHipster logo
```

**注意**：`generator-base.js`中的函数和`generator-constants.js`中的变量是公共API的一部分，因此将遵循semver versioning。但是其他文件（例如`generator-base-private.js`，`utils.js`等）将不会遵循semver versioning，并且可能会在次要版本中破坏方法签名。

## 运行本地蓝图版本进行开发

在开发蓝图期间，请注意以下步骤。他们非常重要。

1. 全局链接您的蓝图

注意：如果您不想将蓝图（第3步）链接到正在创建的每个项目，请使用NPM而不是Yarn，因为yeoman似乎无法获取全局链接的Yarn模块。另一方面，这意味着您还必须在以下所有步骤中使用NPM。

```bash
cd my-blueprint
npm link
```

2. 将JHipster的开发版本链接到您的蓝图（可选：仅当您要使用未发布的JHipster版本（例如master分支或您自己的自定义fork）时才需要）

```bash
cd generator-jhipster
npm link

cd my-blueprint
npm link generator-jhipster
```

3. 为要生成的应用程序创建一个新文件夹，并在其中链接JHipster和您的蓝图

```bash
mkdir my-app && cd my-app

npm link generator-jhipster-myblueprint
npm link generator-jhipster (Optional: Needed only if you are using a non-released JHipster version)

jhipster -d --blueprint myblueprint
```

## 向JHipster市场注册蓝图

要使模块在[JHipster市场]({{ site.url }}/modules/marketplace/)上可用，您需要确保在发布的`package.json`中有2个关键字：`yeoman-generator` 和`jhipster-module`。
如果您在市场中找到不是JHipster模块的任何条目，则可以通过对[jhipster/jhipster.github.io项目](https://github.com/jhipster/jhipster.github.io)进行Pull Request，将其添加到[modules-config.json文件](https://github.com/jhipster/jhipster.github.io/blob/master/modules/marketplace/data/modules-config.json)的`blacklistedModules`部分中，从而将其列入黑名单。

将蓝图发布到NPM后，您的蓝图将在我们的市场上可用。