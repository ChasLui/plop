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

### 绕过提示

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

如果您已经发布了第3方查询者提示插件，并且想为即装即用用户提供旁路功能，[则本文档的另一部分将](#3rd-party-prompt-bypass)对此进行介绍。

### 绕过提示(按名称)
还可以通过使用 `--` 名称绕过提示，然后为希望绕过的每个提示提供参数。[下面](#bypass-examples)的例子。

#### 绕过示例
```
## 绕过提示符 1 和 2
$ plop component "my component" react
$ plop component -- --name "my component" --type react

## 仅跳过提示符 2 (将提示名称)
$ plop component _ react
$ plop component -- --type react
```

### 强制运行生成器
默认情况下，Plop动作可以让你的文件安全，当事情看起来可疑时失败。
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
[**setActionType**](#setactiontype) | *String, [CustomAction](#functionsignature-custom-action)* | | 注册一个自定义操作类型
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
`setPartial` 直接对应于 handlebars 方法`registerPartial`。 因此，如果您熟悉[handlebars partials](https://handlebarsjs.com/guide/partials.html)，那么您已经知道它是如何工作的。

``` javascript
module.exports = function (plop) {
	plop.setPartial('myTitlePartial', '<h1>{{titleCase name}}</h1>');
	// 在模板中用作 {{> myTitlePartial }}
};
```

## setActionType
`setActionType` 允许你创建自己的动作(类似于`add`或`modify`)，可以在你的 plopfiles 中使用。它们基本上是高度可重用的[自定义动作函数](#custom-action-function-)。

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

	// 或在动作中执行异步操作
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
配置对象需要包含 `prompts` 和`actions` （`description` 是可选的）。
提示数组将传递给[inquirer](https://github.com/SBoudrias/Inquirer.js/#objects)。
`actions`数组是要执行的操作的列表（下面将详细介绍）

### *Interface* `GeneratorConfig`
属性 | 类型 | 默认值 | 说明
-------- | ---- | ------- | -----------
**description** | *[String]* | | 对这个生成器的简短描述
**prompts** | *Array[[InquirerQuestion](https://github.com/SBoudrias/Inquirer.js/#question)]* | | 向用户询问的问题
**actions** | *Array[[ActionConfig](#interface-actionconfig)]* | | 要执行的操作

> 如果您的操作列表需要动态显示，请查看[使用动态动作数组](#使用动态动作数组)。

### *Interface* `ActionConfig`
The following properties are the standard properties that plop handles internally. Other properties will be required depending on the *type* of action. Also take a look at the [built-in actions](#built-in-actions).

属性 | 类型 | 默认值 | 说明
-------- | ---- | ------- | -----------
**type** | *String* | | the type of action ([`add`](#add), [`modify`](#modify), [`addMany`](#addmany), [etc](#setactiontype))
**force** | *Boolean* | `false` | performs the action [forcefully](#running-a-generator-forcefully) (means different things depending on the action)
**data** | *Object / Function* | `{}` | specifies data that should be mixed with user prompt answers when running this action
**abortOnFail** | *Boolean* | `true` | if this action fails for any reason abort all future actions
**skip** | *Function* | | an optional function that specifies if the action should run

> The `data` property on any `ActionConfig` can also be a `Function` that returns an `Object` or a `Function` that returns a `Promise` that resolves with an `Object`.

> The `skip` function on any `ActionConfig` is optional and should return a string if the action should be skipped. The return value is the reason to skip the action.

> Instead of an Action Object, a [function can also be used](#custom-action-function-)

## 其他方法
方法 | 参数 | 返回值 | 说明
------ | ---------- | ------- | -----------
**getHelper** | *String* | *Function* | get the helper function
**getHelperList** | | *Array[String]* | get a list of helper names
**getPartial** | *String* | *String* | get a handlebars partial by name
**getPartialList** | | *Array[String]* | get a list of partial names
**getActionType** | *String* | *[CustomAction](#functionsignature-custom-action)* | get an actionType by name
**getActionTypeList** | | *Array[String]* | get a list of actionType names
**setWelcomeMessage** | *String* | | Customizes the displayed message that asks you to choose a generator when you run `plop`.
**getGenerator** | *String* | *[GeneratorConfig](#interface-generatorconfig)* | get the [GeneratorConfig](#interface-generatorconfig) by name
**getGeneratorList** | | *Array[Object]* | gets an array of generator names and descriptions
**setPlopfilePath** | *String* | | set the `plopfilePath` value which is used internally to locate resources like template files
**getPlopfilePath** | | *String* | returns the absolute path to the plopfile in use
**getDestBasePath** | | *String* | returns the base path that is used when creating files
**setDefaultInclude** | *Object* | *Object* | sets the default config that will be used for this plopfile if it is consumed by another plopfile using `plop.load()`
**getDefaultInclude** | *String* | *Object* | gets the default config that will be used for this plopfile if it is consumed by another plopfile using `plop.load()`
**renderString** | *String, Object* | *String* | Runs the first parameter (*String*) through the handlebars template renderer using the second parameter (*Object*) as the data. Returns the rendered template.

# Built-In Actions
There are several types of built-in actions you can use in your [GeneratorConfig](#interface-generatorconfig). You specify which `type` of action  (all paths are based on the location of the plopfile), and a template to use.

> The `Add`, `AddMany` and `Modify` actions have an optional `transform` method that can be used to transform the template result before it is written to disk. The `transform` function receives the template result or file contents as a `string` and the action data as arguments. It must return a `string` or a `Promise` that resolves to a `string`.

## Add
The `add` action is used to (you guessed it) add a file to your project. The path property is a handlebars template that will be used to create the file by name. The file contents will be determined by the `template` or `templateFile` property.

Property | Type | Default | Description
-------- | ---- | ------- | -----------
**path** | *String* | | a handlebars template that (when rendered) is the path of the new file
**template** | *String* | | a handlebars template that should be used to build the new file
**templateFile** | *String* | | a path a file containing the `template`
**skipIfExists** | *Boolean* | `false` | skips a file if it already exists (instead of failing)
**transform** | *Function* | | [an optional function](#built-in-actions) that can be used to transform the template result before writing the file to disk
**skip** | *Function* | | *inherited from [ActionConfig](#interface-actionconfig)*
**force** | *Boolean* | `false` | *inherited from [ActionConfig](#interface-actionconfig)* (overwrites files if they exist)
**data** | *Object* | `{}` | *inherited from [ActionConfig](#interface-actionconfig)*
**abortOnFail** | *Boolean* | `true` | *inherited from [ActionConfig](#interface-actionconfig)*

## AddMany
The `addMany` action can be used to add multiple files to your project with a single action. The `destination` property is a handlebars template that will be used to identify the folder that the generated files should go into. The `base` property  can be used to alter what section of the template paths should be omitted when creating files. The paths located by the `templateFiles` glob can use handlebars syntax in their file/folder names if you'd like the added file names to be unique (example: `{{ dashCase name }}.spec.js`).

Property | Type | Default | Description
-------- | ---- | ------- | -----------
**destination** | *String* | | a handlebars template that (when rendered) is the destination folder for the new files
**base** | *String* | | the section of the path that should be excluded when adding files to the `destination` folder
**templateFiles** | *[Glob](https://github.com/sindresorhus/globby#globbing-patterns)* | | glob pattern that matches multiple template files to be added
**stripExtensions** | *[String]* | `['hbs']` | file extensions that should be stripped from `templateFiles` files names while being added to the `destination`
**globOptions** | *[Object](https://github.com/sindresorhus/globby#options)* | | glob options that change how to match to the template files to be added
**verbose** | *Boolean* | `true` | print each successfully added file path
**transform** | *Function* | | [an optional function](#built-in-actions) that can be used to transform the template result before writing each file to disk
**skip** | *Function* | | *inherited from [ActionConfig](#interface-actionconfig)*
**skipIfExists** | *Boolean* | `false` | *inherited from [Add](#add)* (skips a file if it already exists)
**force** | *Boolean* | `false` | *inherited from [ActionConfig](#interface-actionconfig)* (overwrites files if they exist)
**data** | *Object* | `{}` | *inherited from [ActionConfig](#interface-actionconfig)*
**abortOnFail** | *Boolean* | `true` | *inherited from [ActionConfig](#interface-actionconfig)*

## Modify
The `modify` action can be used two ways. You can use a `pattern` property to find/replace text in the file located at the `path` specified, or you can use a `transform` function to transform the file contents. Both `pattern` and `transform` can be used at the same time (`transform` will happen last). More details on modify can be found in the example folder.

Property | Type | Default | Description
-------- | ---- | ------- | -----------
**path** | *String* | | handlebars template that (when rendered) is the path of the file to be modified
**pattern** | *RegExp* | _end&#x2011;of&#x2011;file_ | regular expression used to match text that should be replaced
**template** | *String* | | handlebars template that should replace what was matched by the `pattern`. capture groups are available as $1, $2, etc
**templateFile** | *String* | | path a file containing the `template`
**transform** | *Function* | | [an optional function](#built-in-actions) that can be used to transform the file before writing it to disk
**skip** | *Function* | | *inherited from [ActionConfig](#interface-actionconfig)*
**data** | *Object* | `{}` | *inherited from [ActionConfig](#interface-actionconfig)*
**abortOnFail** | *Boolean* | `true` | *inherited from [ActionConfig](#interface-actionconfig)*

## Append
The `append` action is a commonly used subset of `modify`. It is used to append data in a file at a particular location.

Property | Type | Default | Description
-------- | ---- | ------- | -----------
**path** | *String* | | handlebars template that (when rendered) is the path of the file to be modified
**pattern** | *RegExp, String* | | regular expression used to match text where the append should happen
**unique** | *Boolean* | `true` | whether identical entries should be removed
**separator** | *String* | `new line` | the value that separates entries
**template** | *String* | | handlebars template to be used for the entry
**templateFile** | *String* | | path a file containing the `template`
**data** | *Object* | `{}` | *inherited from [ActionConfig](#interface-actionconfig)*
**abortOnFail** | *Boolean* | `true` | *inherited from [ActionConfig](#interface-actionconfig)*

## Custom (Action Function)
The `Add` and `Modify` actions will take care of almost every case that plop is designed to handle. However, plop does offer custom action functions for the node/js guru. A custom action function is a function that is provided in the actions array.
- Custom action functions are executed by plop with the same [CustomAction](#functionsignature-custom-action) function signature.
- Plop will wait for the custom action to complete before executing the next action.
- The function must let plop known what’s happening through the return value. If you return a `Promise`, we won’t start other actions until the promise resolves. If you return a message (*String*), we know that the action is done and we’ll report the message in the status of the action.
- A custom action fails if the promise is rejected, or the function throws an `Exception`

_See the [example plopfile](https://github.com/amwmedia/plop/blob/master/example/plopfile.js) for a sample synchronous custom action._

## Comments
Comment lines can be added to the actions array by adding a string in place of an action config object. Comments are printed to the screen when plop comes to them and have no functionality of their own.

# Built-In Helpers
There are a few helpers that I have found useful enough to include with plop. They are mostly case modifiers, but here is the complete list.

## Case Modifiers
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

## Other Helpers
- **pkg**: look up a property from a package.json file in the same folder as the plopfile.

# Taking it Further

There is not a lot needed to get up and running on some basic generators. However, if you want to take your plop-fu further, read on young padawan.

## Using a Dynamic Actions Array
Alternatively, the `actions` property of the [GeneratorConfig](#interface-generatorconfig) can itself be a function that takes the answers data as a parameter and returns the actions array.

This allows you to adapt the actions array based on provided answers:

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

## 3rd Party Prompt Bypass
If you have written an inquirer prompt plugin and want to support plop's bypass functionality, the process is pretty simple. The plugin object that your prompt exports should have a `bypass` function. This `bypass` function will be run by plop with the user's input as the first parameter and the prompt config object as the second parameter. The value that this function returns will be added to the answer data object for that prompt.

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
> For the above example, the bypass function takes the user's text input and turns it into a `Boolean` value that will be used as the prompt answer data.

### Adding Bypass Support to Your Plopfile
If the 3rd party prompt plugin you are using does not support bypass by default, you can add the `bypass` function above to your prompt's config object and plop will use it for handling bypass data for that prompt.

## Wrapping Plop

Plop provides a lot of powerful functionality "for free". This utility is so powerful, in fact, that you can even wrap `plop`
into your own CLI project. To do so, you only need a `plopfile.js`, a `package.json`, and a template to reference.

Your `index.js` file should look like the following:

```javascript
#!/usr/bin/env node
const path = require('path');
const args = process.argv.slice(2);
const {Plop, run} = require('plop');
const argv = require('minimist')(args);

Plop.launch({
  cwd: argv.cwd,
  // In order for `plop` to always pick up the `plopfile.js` despite the CWD, you must use `__dirname`
  configPath: path.join(__dirname, 'plopfile.js'),
  require: argv.require,
  completion: argv.completion
// This will merge the `plop` argv and the generator argv.
// This means that you don't need to use `--` anymore
}, env => run(env, undefined, true));
```

> Be aware that if you choose to use the `env => run(env, undefined, true))`, you may run into command merging issues
> when using generator arg passing.
>
> If you'd like to opt-out of this behavior and act like plop does (requiring `--` before passing named arguments to generators)
> simply replace the `env =>` arrow function with `run`:
>
>```javascript
>Plop.launch({}, run);
>```

And your `package.json` should look like the following:

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

### Setting the base destination path for the wrapper

When wrapping plop, you might want to have the destination path to be based on the cwd when running the wrapper. You can configure the `dest` base path like this:

```javascript
Plop.launch({
	// config like above
}, env => {
	const options = {
		...env,
		dest: process.cwd() // this will make the destination path to be based on the cwd when calling the wrapper
	}
	return run(options, undefined, true)
})
```

### Adding General CLI Actions

Many CLI utilities handle some actions for you, such as running `git init` or `npm install` once the template is generated.

While we'd like to provide these actions, we also want to keep the core actions limited in scope. As such, we maintain a collection of libraries built to add these actions to Plop in [our Awesome Plop list](https://github.com/plopjs/awesome-plop). There, you'll be able to find options for those actions, or even build your own and add it to the list!

### Further Customization

While `plop` provides a great level of customization for CLI utility wrappers, there may be usecases where you simply
want more control over the CLI experience while also utilizing the template generation code.

Luckily, [`node-plop`](https://github.com/plopjs/node-plop/) may be for you! It's what the `plop` CLI itself is built
upon and can be easily extended for other usage in the CLI. However, be warned, documentation is not quite as fleshed out
for integration with `node-plop`. That is to say `Thar be dragons`.

> We note lackluster documentation on `node-plop` integration not as a point of pride, but rather a word of warning.
> If you'd like to contribute documentation to the project, please do so! We always welcome and encourage contributions!
