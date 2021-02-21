Plop
======

微型生成器框架，使整个团队能够轻松地创建具有一致性的文件。
![plop demo](https://i.imgur.com/penUFkr.gif)

> [文档也可以在 plopjs.com 上找到](https://plopjs.com/documentation/)

# 入门指南
[![npm](https://img.shields.io/npm/dm/plop.svg)](https://www.npmjs.com/package/plop)
&nbsp;
[![npm](https://img.shields.io/npm/v/plop.svg)](https://www.npmjs.com/package/plop)
&nbsp;
[![plop on slack](https://img.shields.io/badge/slack-join%20workspace-green)](https://join.slack.com/t/plopjs/shared_invite/zt-ehh69el1-2_DjgZRuMbpC9RnEa4M8cA)

## Plop是什么？

我喜欢把 Plop 称为“微型生成器框架”。
现在，我称其为“小工具”，因为它为您提供了一种以一致的方式生成代码或任何其他类型的纯文本文件的简单方法。
您会看到，我们都在代码中创建了结构和模式（路由，控制器，组件，帮助程序等）。
这些模式会随着时间的推移而变化和改进，因此当您需要在此处创建新的“在模式中插入名称”时，在代码库中查找代表当前“最佳实践”的文件并不总是那么容易。 那就是挽救您的地方。 
使用 plop，您将拥有在代码中创建任何给定模式的“最佳实践”方法。 可以通过键入`plop`从终端轻松运行代码。
这不仅使您免于在代码库中四处寻找要复制的正确文件，而且还使“正确的方式”变成了“创建新文件的最简单的方式”。

如果将 plop 归结为它的核心，它基本上是 [inquirer](https://github.com/SBoudrias/Inquirer.js/) 程序提示符和
[handlebar](https://github.com/wycats/handlebars.js/) 模板之间的粘合代码。

> 这个文档是一个正在进行的工作。如果你有好主意，我很乐意听听。

## 安装
### 1. 将plop添加到您的项目中
```
$ npm install --save-dev plop
```
### 2. 全局安装plop(可选，但建议, 以便于访问)
```
$ npm install -g plop
```
### 3. 在项目的根目录下创建一个 `plopfile.js`
``` javascript
module.exports = function (plop) {
	// 这里创建自己的生成器
	plop.setGenerator('basics', {
		description: '这是一个骨架 plopfile',
		prompts: [], // inquirer 程序提示符数组
		actions: []  // 操作数据
	});
};
```

## 你的第一个Plopfile
plopfile作为一个低级的节点模块开始它的生命，它导出一个函数，该函数接受 `plop` 对象作为它的第一个参数。

``` javascript
module.exports = function (plop) {};
```

`plop` 对象暴露了包含 `setGenerator(name, config)` 函数的 plop api 对象。
这是用于(等待)为这个 plopfile 创建生成器的函数。
当在这个目录(或任何子目录)的终端中运行 `plop` 时，将显示这些生成器的列表。

让我们尝试设置一个基本的生成器，看看效果如何。

``` javascript
module.exports = function (plop) {
	// 生成器的控制器
	plop.setGenerator('controller', {
		description: '应用程序控制器逻辑',
		prompts: [{
			type: 'input',
			name: 'name',
			message: '请输入控制器名称'
		}],
		actions: [{
			type: 'add',
			path: 'src/{{name}}.js',
			templateFile: 'plop-templates/controller.hbs'
		}]
	});
};
```

我们上面创建的*控制器*生成器将问我们一个问题，并创建一个文件。
这可以扩展为根据需要提出尽可能多的问题，并创建尽可能多的文件。
还有一些额外的操作可以用来以不同的方式改变我们的代码库。

## 使用提示

Plop使用[inquirer.js](https://github.com/SBoudrias/Inquirer.js)库来收集用户数据。
在 inquirer.js 官网可以找到[prompt types](https://github.com/SBoudrias/Inquirer.js/#prompt-types)列表。

## CLI 使用

安装了 plop 并创建了一个生成器之后，就可以从终端运行plop了。
运行不带参数的 plop 将提供一个生成器列表供您选择。
你也可以运行 `plop [generatorName]` 来直接触发一个生成器。
如果你没有全局安装 plop，你需要设置一个 npm 脚本来运行 plop。

```javascript
// package.json
{
    ...,
    "scripts": {
        "plop": "plop"
    },
    ...
}
```

### 旁路提示

一旦您很好地了解了一个项目(及其生成器)，您可能希望在运行生成器时为提示提供答案。
例如，如果我有一个有一个提示符(名称)的 `component` 生成器，我可以使用 `plop component "some component name"` 运行该生成器，
它会立即执行，就像我在提示符中输入了"some component name"一样。
如果同一个生成器有第二个提示符，那么同样的输入将会提示用户输入第二个值。

像 `confirm` 和`list`这样的提示尽量让你的输入有意义。
例如，输入“y”，“yes”，“t”，或“true”作为确认提示符将会得到一个布尔值。
您可以使用项的值、索引、键或名称从列表中选择项。
复选框提示可以接受用逗号分隔的值列表，以便选择多个值。

![plop bypass demo](https://media.giphy.com/media/3ov9jQ38ypmX4SuT60/giphy.gif)

> 如果你想为第二个提示符而不是第一个提示符提供旁路输入，
> 你可以使用下划线 "\_" 来跳过旁路(即`plop component _ "input for second prompt"`)。

Plop 为标准的 inquirer 提示提供了内置的旁路逻辑，但是也有一些方法可以提供自定义的逻辑来处理特定提示的用户输入。

如果您已经发布了第3方查询者提示插件，并且想为即装即用用户提供旁路功能，[则本文档的另一部分将](#第三方提示旁路)对此进行介绍。

### 旁路提示(按名称)
还可以通过使用 `--` 名称旁路提示，然后为希望旁路的每个提示提供参数。[下面](#bypass-examples)的例子。

#### 旁路示例
```
## 旁路提示符 1 和 2
$ plop component "my component" react
$ plop component -- --name "my component" --type react

## 仅跳过提示符 2 (将提示名称)
$ plop component _ react
$ plop component -- --type react
```

### 强制运行生成器
默认情况下，Plop操作可以让你的文件安全，当事情看起来可疑时失败。
最明显的例子是不允许[`add`](#add)操作覆盖已经存在的文件。
Plop操作单独支持 `force` 属性，但你也可以在终端运行 Plop 时使用 `--force` 标志。
使用 `--force` 标志将告诉每个行动都必须强力执行。有大能力……🕷

## 为什么要使用生成器？
因为当您从代码中独立创建样板文件时，您自然会投入更多的时间和思考。

因为当创建每个路由、组件、控制器、助手、测试、视图等时，可以为你的团队(或你自己)节省 5 - 15 分钟, [真的加起来](https://xkcd.com/1205/)。

因为[上下文切换非常昂贵](https://www.petrikainulainen.net/software-development/processes/the-cost-of-context-switching/)，并且节省时间并不是[自动化工作流程](https://kentcdodds.com/blog/automation)的唯一好处

# Plopfile Api

plopfile api 是由 `plop` 对象公开的方法集合。大部分工作是由[`setGenerator`](#setgenerator)完成的，但本节介绍了 plopfile 中可能也有用的其他方法。

## TypeScript 声明

`plop` 绑定TypeScript声明。
不管你是否用 TypeScript 编写plopfile，很多编辑器都会通过这些声明提供代码帮助。

```javascript
// plopfile.ts
import {NodePlopAPI} from 'plop';

export default function (plop: NodePlopAPI) {
  // plop 生成器代码
};
```

```javascript
// plopfile.js
module.exports = function (
	/** @type {import('plop').NodePlopAPI} */
	plop
) {
	// plop 生成器代码
};
```

## 主要方法
这些是在创建 plopfile 时常用的方法。其他主要用于内部使用的方法在[其他方法](#其他方法)一节中列出。

方法 | 参数 | 返回 | 说明
------ | ---------- | ------- | -----------
[**setGenerator**](#setgenerator) | *String, [GeneratorConfig](#interface-generatorconfig)* | *[GeneratorConfig](#interface-generatorconfig)* | 设置一个生成器
[**setHelper**](#sethelper) | *String, Function* | | 设置一个 handlebars 助手
[**setPartial**](#setpartial) | *String, String* | | 设置一个 handlebars 部分
[**setActionType**](#setactiontype) | *String, [CustomAction](#functionsignature-自定义操作)* | | 注册一个自定义操作类型
[**setPrompt**](#setprompt) | *String, InquirerPrompt* | | registers a custom prompt type with inquirer
[**load**](https://github.com/amwmedia/plop/blob/master/plop-load.md) | *Array[String], Object, Object* | | loads generators, helpers and/or partials from another plopfile or npm module

## setHelper

`setHelper` 直接对应于 handlebars 方法`registerHelper`。 因此，如果您熟悉[handlebars helpers](https://handlebarsjs.com/guide/expressions.html#helpers)，那么您已经知道它是如何工作的。

``` javascript
module.exports = function (plop) {
	plop.setHelper('upperCase', function (text) {
		return text.toUpperCase();
	});

	// 或者在 es6/es2015
	plop.setHelper('upperCase', (txt) => txt.toUpperCase());
};
```

## setPartial
`setPartial` 直接对应于 handlebars 方法 `registerPartial`。 因此，如果您熟悉[handlebars partials](https://handlebarsjs.com/guide/partials.html)，那么您已经知道它是如何工作的。

``` javascript
module.exports = function (plop) {
	plop.setPartial('myTitlePartial', '<h1>{{titleCase name}}</h1>');
	// 在模板中用作 {{> myTitlePartial }}
};
```

## setActionType
`setActionType` 允许你创建自己的操作(类似于`add`或`modify`)，可以在你的 plopfiles 中使用。它们基本上是高度可重用的[自定义操作函数](#custom-action-function-)。

### *FunctionSignature* 自定义操作
参数 | 类型 | 说明
---------- | ---- | -----------
**answers** | *Object* | 生成器提示的答案
**config** | *[ActionConfig](#interface-actionconfig)* | 生成器的 `actions` 数组中的对象
**plop** | *[PlopfileApi](#plopfile-api)* | 这个操作正在运行的 plopfile 的 plop api

``` javascript
module.exports = function (plop) {

	plop.setActionType('doTheThing', function (answers, config, plop) {
		// 做点什么
		doSomething(config.configProp);
		// 如果出了什么问题
		throw '错误消息';
		// 否则
		return '成功状态消息';
	});

	// 或在操作中执行异步操作
	plop.setActionType('doTheAsyncThing', function (answers, config, plop) {
		// 做点什么
		return new Promise((resolve, reject) => {
			if (success) {
				resolve('成功状态消息');
			} else {
				reject('error message');
			}
		});
	});

	// 使用自定义操作
	plop.setGenerator('test', {
		prompts: [],
		actions: [{
			type: 'doTheThing',
			configProp: 'available from the config param'
		}, {
			type: 'doTheAsyncThing',
			speed: 'slow'
		}]
	});
};
```

## setPrompt
[Inquirer](https://github.com/SBoudrias/Inquirer.js) 提供了许多开箱即用的提示类型，但它也允许开发人员构建提示插件。
如果你想使用一个提示插件，你可以用`setPrompt`注册它。
有关更多详细信息，请参见[用于注册提示的查询者文档](https://github.com/SBoudrias/Inquirer.js#inquirerregisterpromptname-prompt)。
另请查看[plop社区驱动的自定义提示列表](https://github.com/amwmedia/plop/blob/master/inquirer-prompts.md)。

``` javascript
const promptDirectory = require('inquirer-directory');
module.exports = function (plop) {
	plop.setPrompt('directory', promptDirectory);
	plop.setGenerator('test', {
		prompts: [{
			type: 'directory',
			...
		}]
	});
};
```

## setGenerator
配置对象需要包含 `prompts` 和 `actions` （`description` 是可选的）。
提示数组将传递给 [inquirer](https://github.com/SBoudrias/Inquirer.js/#objects)。
`actions` 数组是要执行的操作的列表（下面将详细介绍）

### *Interface* `GeneratorConfig`
属性 | 类型 | 默认值 | 说明
-------- | ---- | ------- | -----------
**description** | *[String]* | | 对这个生成器的简短描述
**prompts** | *Array[[InquirerQuestion](https://github.com/SBoudrias/Inquirer.js/#question)]* | | 向用户询问的问题
**actions** | *Array[[ActionConfig](#interface-actionconfig)]* | | 要执行的操作

> 如果您的操作列表需要动态显示，请查看[使用动态操作数组](#使用动态操作数组)。

### *Interface* `ActionConfig`
下面的属性是plop内部处理的标准属性。根据操作的*类型*，还需要其他属性。再看一下[内置操作](#内置操作)。

属性 | 类型 | 默认值 | 说明
-------- | ---- | ------- | -----------
**type** | *String* | | 操作类型 ([`add`](#add), [`modify`](#modify), [`addMany`](#addmany), [etc](#setactiontype))
**force** | *Boolean* | `false` | [强制执行操作](#强制运行生成器)（根据操作表示不同的意思）
**data** | *Object / Function* | `{}` | 指定在运行此操作时应与用户提示答案混合的数据
**abortOnFail** | *Boolean* | `true` | 如果此操作由于任何原因失败，则中止所有将来的操作
**skip** | *Function* | | 一个可选的函数，指定操作是否应该运行

> 任何 `ActionConfig` 上的 `data` 属性也可以是一个返回 `Object` 的 `Function` ，
> 或者一个返回 `Promise` 的 `Function` ，它可以用 `Object`来解析。

> 任何 `ActionConfig` 上的 `skip` 函数都是可选的，如果该操作应该被跳过，
> 则应该返回一个字符串。返回值是跳过该操作的原因。

> 除了 Action 对象，[还可以使用函数](#自定义-操作函数-)

## 其他方法
方法 | 参数 | 返回值 | 说明
------ | ---------- | ------- | -----------
**getHelper** | *String* | *Function* | 获取一个助手函数
**getHelperList** | | *Array[String]* | 获取一个助手名称列表
**getPartial** | *String* | *String* | 按名字取 handlebars 局部
**getPartialList** | | *Array[String]* | 获取一个局部列表
**getActionType** | *String* | *[CustomAction](#functionsignature-自定义操作)* | 通过名称获取一个 actionType
**getActionTypeList** | | *Array[String]* | 获取 actionType 名称列表
**setWelcomeMessage** | *String* | | 自定义在运行 `plop` 时要求您选择生成器的显示消息。
**getGenerator** | *String* | *[GeneratorConfig](#interface-generatorconfig)* | 通过名称获取 [GeneratorConfig](#interface-generatorconfig)
**getGeneratorList** | | *Array[Object]* | 获取生成器名称和描述的数组
**setPlopfilePath** | *String* | | 设置 `plopfilePath` 值，该值在内部用于定位模板文件等资源
**getPlopfilePath** | | *String* | 返回正在使用的 plopfile 的绝对路径
**getDestBasePath** | | *String* | 返回创建文件时使用的基本路径
**setDefaultInclude** | *Object* | *Object* | 设置默认配置，如果这个 plopfile 被另一个 plopfile 使用，使用 `plop.load()`
**getDefaultInclude** | *String* | *Object* | 获取默认配置，如果该配置文件被另一个 plopfile 使用 `plop.load()` 使用，它将用于该plopfile。
**renderString** | *String, Object* | *String* | 使用第二个参数（*Object*）作为数据，通过 handlebars 模板渲染器运行第一个参数（*String*）。 返回渲染的模板。

# 内置操作
你可以在你的[GeneratorConfig](#interface-generatorconfig)中使用几种类型的内置操作。
你可以指定操作的 `type` (所有路径都基于 plopfile 的位置)和要使用的模板。

> `Add`， `AddMany` 和 `Modify` 操作有一个可选的 `transform` 方法，可用于在模板结果被写入磁盘之前转换它。
> `transform` 函数以 `string`形式接收模板结果或文件内容，操作数据作为参数。
> 它必须返回一个 `string` 或一个 `Promise` 来解析为 `string`。
## Add
 `add` 操作用于(你猜到了)将文件添加到项目中。path 属性是一个 handlebars 模板，用于按名称创建文件。文件内容将由 `template` 或 `templateFile` 属性决定。

属性 | 类型 | 默认值 | 说明
-------- | ---- | ------- | -----------
**path** | *String* | | 一个 handlebars 模板（渲染时）是新文件的路径
**template** | *String* | | 应该用于构建新文件的 handlebars 模板
**templateFile** | *String* | | 包含 `template` 的文件的路径
**skipIfExists** | *Boolean* | `false` | 如果文件已经存在，则跳过它(而不是失败)
**transform** | *Function* | | [可选功能](#内置操作) ，可用于在将文件写入磁盘之前转换模板结果
**skip** | *Function* | | *继承自 [ActionConfig](#interface-actionconfig)*
**force** | *Boolean* | `false` | *继承自 [ActionConfig](#interface-actionconfig)* (如果文件存在，则覆盖文件)
**data** | *Object* | `{}` | *继承自 [ActionConfig](#interface-actionconfig)*
**abortOnFail** | *Boolean* | `true` | *继承自 [ActionConfig](#interface-actionconfig)*

## AddMany
`addMany` 操作可用于通过一个操作将多个文件添加到项目中。  `destination` 属性是一个 handlebars 模板，将用于标识生成的文件应放入的文件夹。  `base` 属性可用于更改在创建文件时应忽略模板路径的哪一部分。 如果您希望添加的文件名是唯一的，那么 `templateFiles` 全局文件所位于的路径可以在它们的文件/文件夹名称中使用 handlebars 语法（例如：`{{ dashCase name }}.spec.js`）。

属性 | 类型 | 默认值 | 说明
-------- | ---- | ------- | -----------
**destination** | *String* | | handlebars模板(在渲染时)是新文件的目标文件夹
**base** | *String* | | 向 `destination` 文件夹添加文件时应排除的路径部分
**templateFiles** | *[Glob](https://github.com/sindresorhus/globby#globbing-patterns)* | | 与要添加的多个模板文件匹配的全局模式
**stripExtensions** | *[String]* | `['hbs']` | 当被添加到 `destination` 时，应该从 `templateFiles` 文件名中剥离的文件扩展名
**globOptions** | *[Object](https://github.com/sindresorhus/globby#options)* | | 全局选项，用于更改与要添加的模板文件的匹配方式
**verbose** | *Boolean* | `true` | 打印每个成功添加的文件路径
**transform** | *Function* | | [可选功能](#内置操作) 可以在将每个文件写入磁盘之前转换模板结果
**skip** | *Function* | | *继承自 [ActionConfig](#interface-actionconfig)*
**skipIfExists** | *Boolean* | `false` | *继承自 [Add](#add)* (跳过已经存在的文件)
**force** | *Boolean* | `false` | *继承自 [ActionConfig](#interface-actionconfig)* (如果文件存在，则覆盖文件)
**data** | *Object* | `{}` | *继承自 [ActionConfig](#interface-actionconfig)*
**abortOnFail** | *Boolean* | `true` | *继承自 [ActionConfig](#interface-actionconfig)*

## Modify
可以通过两种方式使用 `modify` 操作。 您可以使用 `pattern` 属性在指定的 `path` 中查找/替换文件中的文本，也可以使用 `transform` 函数来转换文件内容。  `pattern` 和 `transform` 都可以同时使用（ `transform` 将最后发生）。 可以在 example 文件夹中找到有关修改的更多详细信息。

属性 | 类型 | 默认值 | 说明
-------- | ---- | ------- | -----------
**path** | *String* | | handlebars模板(在渲染时)是要修改的文件的路径
**pattern** | *RegExp* | _end&#x2011;of&#x2011;file_ | 用于匹配应替换文本的正则表达式
**template** | *String* | | handlebars 模板，应该取代什么是匹配的`pattern`。捕获组有 $1、$2 等
**templateFile** | *String* | | 路径包含 `template` 的文件
**transform** | *Function* | | [可选功能](#内置操作) 可用于在将文件写入磁盘之前转换文件
**skip** | *Function* | | *继承自 [ActionConfig](#interface-actionconfig)*
**data** | *Object* | `{}` | *继承自 [ActionConfig](#interface-actionconfig)*
**abortOnFail** | *Boolean* | `true` | *继承自 [ActionConfig](#interface-actionconfig)*

## Append
`append` 操作是 `modify` 的一个常用子集。它用于在文件的特定位置追加数据。

属性 | 类型 | 默认值 | 说明
-------- | ---- | ------- | -----------
**path** | *String* | | handlebars模板(在渲染时)是要修改的文件的路径
**pattern** | *RegExp, String* | | 用于匹配应替换文本的正则表达式
**unique** | *Boolean* | `true` | 是否删除相同的条目
**separator** | *String* | `new line` | 分隔条目的值
**template** | *String* | | 入口使用的 handlebars 模板
**templateFile** | *String* | | 路径包含 `template` 的文件
**data** | *Object* | `{}` | *继承自 [ActionConfig](#interface-actionconfig)*
**abortOnFail** | *Boolean* | `true` | *继承自 [ActionConfig](#interface-actionconfig)*

## 自定义 (操作函数)
`Add` 和 `Modify` 操作几乎可以处理plop旨在处理的每种情况。 但是，plop 确实为 node / js 专家提供了自定义操作功能。 
自定义操作函数是在操作数组中提供的函数。
- 自定义操作函数由 plop 以相同的 [CustomAction](#functionsignature-自定义操作) 函数签名执行。
- Plop将等待自定义操作完成后再执行下一个操作。
- 如果你返回一个 `Promise`，在 Promise 解析之前我们不会启动其他操作。
如果您返回一个消息(*String*)，我们知道操作已经完成，我们将在操作的状态中报告该消息。
- 如果 promise 被拒绝，或者函数抛出 `Exception`，则自定义操作失败

_参见 [plopfile 示例](https://github.com/amwmedia/plop/blob/master/example/plopfile.js)，了解同步自定义操作的示例。_

## 注释
通过在 action 配置对象中添加字符串，可以将注释行添加到 actions 数组中。
当 plop 找到注释时，注释会被打印到屏幕上，而且注释本身没有任何功能。
# 内置 Helpers
我发现有一些非常有用的 Helpers 可以包含在 plop 中。它们大多是大小写修饰符，但下面是完整的列表。

## 案例装饰器
- **camelCase**: changeFormatToThis
- **snakeCase**: change_format_to_this
- **dashCase/kebabCase**: change-format-to-this
- **dotCase**: change.format.to.this
- **pathCase**: change/format/to/this
- **properCase/pascalCase**: ChangeFormatToThis
- **lowerCase**: change format to this
- **sentenceCase**: Change format to this,
- **constantCase**: CHANGE_FORMAT_TO_THIS
- **titleCase**: Change Format To This

## 其他 Helpers
- **pkg**: 从包中查找属性 package.json 文件与 plopfile 在同一个文件夹中。

# 更进一步
使用一些基本的生成器并不需要很多东西。然而，如果你想更进一步，请阅读 young padawan。

## 使用动态操作数组
另外，[GeneratorConfig](#interface-generatorconfig)的 `actions` 属性本身可以是一个函数，它将 answers 数据作为参数并返回 actions 数组。

这允许你根据提供的答案(answers)调整操作数组:

``` javascript
module.exports = function (plop) {
	plop.setGenerator('test', {
		prompts: [{
			type: 'confirm',
			name: 'wantTacos',
			message: 'Do you want tacos?'
		}],
		actions: function(data) {
			var actions = [];

			if(data.wantTacos) {
				actions.push({
					type: 'add',
					path: 'folder/{{dashCase name}}.txt',
					templateFile: 'templates/tacos.txt'
				});
			} else {
				actions.push({
					type: 'add',
					path: 'folder/{{dashCase name}}.txt',
					templateFile: 'templates/burritos.txt'
				});
			}

			return actions;
		}
	});
};
```

## 第三方提示旁路
如果你已经写了一个 inquirer 提示插件，并且想要支持 plop 的旁路功能，这个过程是非常简单的。
提示导出的插件对象应该有一个 `bypass` 函数。
这个`bypass` 函数将由 plop 运行，用户的输入作为第一个参数，提示配置对象作为第二个参数。
该函数返回的值将添加到该提示的答案数据对象中。

``` javascript
// My confirmation inquirer plugin
module.exports = MyConfirmPluginConstructor;
function MyConfirmPluginConstructor() {
	// ...your main plugin code
	this.bypass = (rawValue, promptConfig) => {
		const lowerVal = rawValue.toString().toLowerCase();
		const trueValues = ['t', 'true', 'y', 'yes'];
		const falseValues = ['f', 'false', 'n', 'no'];
		if (trueValues.includes(lowerVal)) return true;
		if (falseValues.includes(lowerVal)) return false;
		throw Error(`"${rawValue}" is not a valid ${promptConfig.type} value`);
	};
	return this;
}
```
> 对于上面的示例，旁路函数接受用户的文本输入，并将其转换为一个 `Boolean` 值，作为提示答案数据。

### 添加旁路支持到您的配置文件
如果你使用的第三方提示插件默认不支持旁路，你可以在提示的配置对象中添加 `bypass` 函数，plop 会使用它来处理该提示的旁路数据。

## 包装 Plop

Plop提供了许多“免费”的强大功能。
这个实用程序非常强大，事实上，你甚至可以将`plop`包装到你自己的 CLI 项目中。
为此，你只需要一个`plopfile.js`, `package.json`，以及一个要引用的模板。

你的 `index.js` 文件应如下所示：

```javascript
#!/usr/bin/env node
const path = require('path');
const args = process.argv.slice(2);
const {Plop, run} = require('plop');
const argv = require('minimist')(args);

Plop.launch({
  cwd: argv.cwd,
  // 为了使 `plop` 在 CWD 情况下始终能够拾取 `plopfile.js`，必须使用 `__dirname`
  configPath: path.join(__dirname, 'plopfile.js'),
  require: argv.require,
  completion: argv.completion
// 这将合并 `plop` argv和生成器 argv。
// 这意味着你不需要再用 `--` 了
}, env => run(env, undefined, true));
```

> 请注意，如果选择使用 `env => run(env, undefined, true))`，在使用生成器参数传递时可能会遇到命令合并问题。
>
> 如果您想选择退出此行为并像 plop 那样行事(要求 `--` 在将命名参数传递给生成器之前)，只需用 `run` 替换 `env =>` 箭头函数:
>
>```javascript
>Plop.launch({}, run);
>```

您的 `package.json` 文件应如下所示：

```json
{
  "name": "create-your-name-app",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "plop",
  },
  "bin": {
    "create-your-name-app": "./index.js"
  },
  "preferGlobal": true,
  "dependencies": {
    "plop": "^2.6.0"
  }
}
```
### 设置包装器的基本目标路径

在包装 plop 时，您可能希望基于运行包装器时的 cwd 来实现目标路径。你可以像这样配置 `dest` 基路径：

```javascript
Plop.launch({
	// 像上面的配置
}, env => {
	const options = {
		...env,
		dest: process.cwd() // 这将使目标路径在调用包装器时基于 cwd
	}
	return run(options, undefined, true)
})
```

### 添加常规 CLI 操作

许多 CLI 实用程序都会为您处理一些操作，例如在生成模板后运行`git init`或 `npm install` 。

尽管我们希望提供这些操作，但我们也希望将核心操作限制在范围内。 
因此，我们维护了一个库集合，这些库旨在将这些操作添加到[Awesome Plop列表](https://github.com/plopjs/awesome-plop)中的 Plop 中。
在这里，您将能够找到这些操作的选项，甚至可以构建自己的操作并将其添加到列表中！
### 进一步自定义

虽然 `plop` 为 CLI 实用程序包装器提供了一个很棒的定制级别，但在有些情况下，你可能只是想要更多地控制 CLI 体验，同时也利用模板生成代码。

幸运的是，[`node-plop`](https://github.com/plopjs/node-plop/) 可能适合你! `plop` CLI本身就是基于它构建的，并且可以很容易地扩展到 CLI 中的其他用途。
但是，需要注意的是，对于与`node-plop`的集成，文档并没有那么充实。
那就是说它们是恶龙。

> 我们注意到，关于 `node-plop` 集成的文档很乏味，不是出于自豪，而是警告。
> 如果您想为该项目提供文档，请这样做！ 我们始终欢迎并鼓励您的贡献！
