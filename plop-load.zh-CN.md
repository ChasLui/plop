plop.load
=========

简体中文 / [English](./plop-load.md)

`plop.load`可用于从项目中或NPM（`plop-pack`）上的其他 plopfiles 加载生成器，actionType，helper 和 partials。 下面的示例代码。

### plop.load([targets](#targets), [[config](#config)], [[include](#include)])

#### targets
- **类型:** `String` 或者 `Array<String>`
- **必填**

`targets` 是要加载的 plopfile 的一个或多个位置。这些位置可以是文件路径(绝对的或相对于当前 plopfile )或应该加载的 `plop-pack` 的节点模块名。

#### config
- **类型:** `Object`
- **可选**

`config` 是一个可以在运行 plopfile 或 `plop-pack` 时传递给它们的对象。
这允许 plopfile 或 `plop-pack` 的使用者配置其功能的某些方面。
要知道这个对象中应该包含哪些属性，请参阅作者提供的文档。

#### include
- **类型:** `Object`
- **默认值:** `{ generators:true, helpers:false, partials:false, actionTypes:false }`
- **可选**

`include` 是一个可以包含4个属性的对象(`generators`, `helpers`, `partials`, 和 `actionTypes`)。每个属性都应该有一个[`IncludeDefinition`](#IncludeDefinition)作为其值。大多数时候这个对象是不需要的，因为 plopfile 或`plop-pack`能够指定一个默认的[`IncludeDefinition`](#IncludeDefinition)来使用。

#### 接口 `IncludeDefinition`
- **Boolean:** `true` 将包括所有资产，`false` 将不包括任何资产。
- **Array:** 应该包含的资产名称列表。任何与列表中名称不匹配的资产都将被跳过。
- **Object:** include 对象允许使用者在自己的 plopfile 中使用时重命名资产。
该对象中的属性名是资产名称，值是加载资产时赋予的名称。

## 例子
*通过路径加载*
```javascript
	// 加载所有5个生成器，不加载任何 helpers，actionTypes 或 partials（即使它们存在）
	plop.load('./plopfiles/component.js');
```
*通过带有自定义包含配置的路径加载*
```javascript
	// 加载所有的helper，非 generator , actiontype 或 partials(即使它们存在)
	plop.load('./plopfiles/component.js', {}, { helpers: true });
```
*通过带有限定 include 的路径进行加载*
```javascript
	// 只加载 "js-header" helper, 非 generators, actionTypes 或 partials(即使它们存在)
	plop.load('./plopfiles/component.js', {}, { helpers: ['js-header'] });
```
*通过带有 config 对象的路径加载*
```javascript
	// component.js 模块将接收config对象并做一些事情：
	// 实例: 它可以使用它来更改它将使用的模板（es6）
	//          并在所有生成器名称前加上'foo'
	plop.load('./plopfiles/component.js', { es6: true, namePrefix: 'foo' });
```
*通过 [npm module](https://www.npmjs.com/package/plop-pack-fancy-comments)* 加载
> 该模块[配置默认的包含定义](https://github.com/amwmedia/plop-pack-fancy-comments/blob/master/index.js#L13)

```javascript
	// 加载所有 3 个 helpers
	plop.load('plop-pack-fancy-comments');
};
```
*通过npm模块加载，并重命名 include 配置*
```javascript
	// 在 plopfile 和模板中只加载头部 helpers，不加载 generators、actionTypes 或 partials(即使它们存在)，
	// “js-header” 助手被引用为 “titleComment”
	plop.load('plop-pack-fancy-comments', {}, { helpers: {'js-header': 'titleComment'} });
```
*通过 npm 模块和路径加载*
```javascript
	// 为每个项目使用默认的 include 配置
	plop.load([
		'plop-pack-fancy-comments',
		'./plopfiles/component.js'
	]);
```
