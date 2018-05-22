# weex

## 1. 快速构建

需要nodejs环境，之前需要安装nodejs

```shell
$ npm install -g weex-toolkit
$ weex -v //查看当前weex版本
```

```shell
# 创建项目
$ weex create awesome-project
$ cd awesome-project
$ npm start
```

可以通过访问http://192.168.0.101:8083/web/preview.html?page=index.js查看渲染中的界面

可以通过扫二维码查看在真机上的效果，需要下载weex软件



##2. 基础特性 

- vuejs：weex无法使用以下形式，它引入的是运行时版本

```javascript
new Vue({
    template:'<div>{{h1}}</div>'
})
Vue.compile('<div>{{msg}}</div>')
```

```javascript
// weex注册事件无法使用stop等事件修饰符
// weex默认不支持事件的冒泡，可以自己手动打开
v-on:click.stop='add'
```

- vue-router与vuex：不推荐使用，因为在安卓端是多页面

- css样式默认是scoped，而且不能简写

```css
/*不支持*/
margin：10px; 
/*必须写成以下形式*/
margin-top：10px；
margin-right:10px;
margin-bottom:10px;
margin-left:10px;
```

- weex支持的所有样式

1. 盒子模式：height、width、padding、border、margin
2. flexbox：flex、flex-direction、justify-content、aligin-items
3. 定位：position（不支持z-index，元素层级默认先后顺序，越靠后层级越高）
4. transform：transiton、scale、rotate、perspective、transfomr-origin
5. 阴影：box-shadow、
6. 透明度：opacity、background-color
7. 文字：color、font-size、font-style、font-weight、text-decoration、text-aligin、font-family、text-overflow
8. 伪类：只支持active、特例是表单中input、textarea支持focus、enabled、disabled 



## 3. 内置组件

### 3.1 text

`<text>` 是 Weex 内置的组件，用来将文本按照指定的样式渲染出来。`<text>` 只能包含文本值，你可以使用 `{{}}` 标记插入变量值作为文本内容 

屏幕上所有文字都应该放到text组件中，它不能包裹其他自组件，font-size默认值32px



### 3.2 a

a标签在native端是可用的，但是在web端有问题，因为它需要的是一个bundle.wx文件，在web打开知识一些js代码

a标签不建议注册click事件的时候，在跳转之前进行逻辑代码判断，因为click事件的回调函数和herf跳转的执行顺序是不可知的



### 3.2 image

属性：

- reszie

1. contain ：缩放图片以完全装入`<image>`区域，可能背景区部分空白 
2. cover ：缩放图片以完全覆盖`<image>`区域，可能图片部分看不见 
3. stretch ：`默认值`. 按照`<image>`区域的宽高比例缩放图片 	

- placeholder ：占位图，图片下载完成就删除
- src：要显示图片的 URL，该属性是 `<image>` 组件的强制属性。 

使用：不支持img标签，使用image必须指定宽高

```html
<image style="width:500px;height:500px" src="https://vuejs.org/images/logo.png"></image>
```

方法：

- save：保存图片内容到本地文件或相册，此操作可能需要设备相关权限。 

- load：当加载完成 `src` 指定的图片时，`load`事件将被触发。



###3.3 indicator

`<indicator>` 组件通常用于显示轮播图指示器效果，必须充当 `<slider>`组件的子组件使用。 

`<indicator>` 组件有一些私有样式，如下：

- `item-color {color}`：indicator指示点未被选中时的颜色，默认值为 `#CCCCCC`
- `item-selected-color {color}`：indicator指示点被选中时的颜色，默认值为 `#444444`
- `item-size {number}`：指示点的半径，默认为 `5px`



### 3.4 input

目前不支持 `this.$el(id).value = ''` 这种方式改写 input value。只支持在 `<input>` 组件的 `input`、`change` 事件中改写 



### 3.5 list

`<list>` 组件是提供垂直列表功能的核心组件，拥有平滑的滚动和高效的内存管理，非常适合用于长列表的展示。最简单的使用方法是在 `<list>` 标签内使用一组由简单数组 `repeat` 生成的 `<cell>` 标签填充。 

- 子组件：

1. `<cell>`
2. 用于定义列表中的子列表项，类似于 HTML 中的 `ul` 之于 `li`。Weex 会对 `<cell>` 进行高效的内存回收以达到更好的性能。
3. `header` 0.6.1+
4. 当 `<header>` 到达屏幕顶部时，吸附在屏幕顶部。
5. `<refresh>`
6. 用于给列表添加下拉刷新的功能。
7. `<loading>`
8. `<loading>` 用法与特性和 `<refresh>` 类似，用于给列表添加上拉加载更多的功能。



### 3.6 recycle-lsit

`<recycle-list> `是一个新的列表容器，具有回收和复用的能力，可以大幅优化内存占用和渲染性能。 

- 基本使用：

原有 `<list>` 和 `<cell>` 组件的功能不受影响，针对新功能提供了新的 `<recycle-list>` 和 `<cell-slot>` 组件。如果想利用列表回收和复用的特性，使用新组件即可 

- 属性：

1. for：在 `<recycle-list>` 添加 `for` 属性即可描述如何循环展开列表的数据，语法和 Vue 的 `v-for`指令类似，但是它循环的是自己内部的子节点，并不是当前节点。 
2. switch：在 `<recycle-list>` 添加 `switch` 属性可以用来指定数据中用于区分子模板类型的字段名，语义和编程语言里的 `switch` 一致，配合 `<cell-slot>` 中的 `case` 和 `default` 属性一起使用。如果省略了 `switch` 属性，则只会将第一个 `<cell-slot>` 视为模板，多余的模板将会被忽略。
3. case：声明了当前模板的类型，只有和数据中的类型与当前类型匹配时才会渲染，语义和编程语言里的 `case` 一致。所有模板中最多只会匹配到一项，按照模板的顺序从上到下匹配，一旦匹配成功就不在继续匹配下一个。
4. default：表示当前模板为默认模板类型，不需要指定值。如果数据项没有匹配到任何 `case` 类型，则渲染带有 `default` 模板。如果存在多个 `default`，则只会使用第一个默认模板。 
5. key：可选属性，用于指定列表数据中可以作为唯一标识的键值，可以优化渲染性能。 

- 实例：

```html
<recycle-list for="(item, i) in longList" switch="type">
  <cell-slot case="A">
    <text>- A {{i}} -</text>
  </cell-slot>
  <cell-slot case="B">
    <text>- B {{i}} -</text>
  </cell-slot>
</recycle-list>
```



### 3.7 scroller

scroller：组件定义一块可以滚动的区域，它在与list使用的时候，记住无能与list具有相同的方向

### 3.8 slider 

slider：组件用于在一个页面中展示多个图片，在前端，这种效果被称为轮播图 

### 3.9 video

视频

### 3.10 waterfall

提供瀑布流布局的组件， 多栏的情况下建议使用，这区别于list，list更像是一个简单的列表展示

### 3.11 web

用于在 weex 页面中显示由 `src` 属性指定的页面内容。您还可以使用 `webview` 模块来控制 WebView 的行为，例如回退、前进和重新加载，更多信息请参见 [webview module](https://weex.incubator.apache.org/cn/references/modules/webview.html)。 



## 4. 内置模块

###4.1 AMD

```javascript
// [alert]:是模块的依赖
define('模块名',['alert'],function(alert){
    return function(){
        alert('这是AMD模块')
    }
})
```

### 4.2 CommonJs

```javascript
var alert = require('alert');
module.exports = function(){
    alert('这是commonJS模块')
}
```

### 4.3 ES

```javascript
import alert from 'alert';
export default function(){
    alert('这是ES模块')
}
```

### 4.4 weex

```javascript
// weex区别于以上三种，它引入了native、ios等方法的形式比较特殊，通过调用这些方法达到操作设备的目的
const animation = weex.requireModule('animation')
animation.transition();
```

### 4.5 module

- animation：动画

- dom：模块用于对 weex 页面里的组件节点进行一部分特定操作。

1. scrollToElement：滚动到对于的dom元素位置
2. getComponentRect：dom元素布局信息
3. addRule：为dom添加规则，比如添加字体样式，ttf，woff，只支持这两种，一般在beforCreate中设置

- gobalEvent：主动监听，例如监听定位，陀螺仪等事件
- meta：设置viewport宽度，默认是750px
- modal：展示消息框
- navigator：导航，解决了web页面中点击效果失败的情况
- picker：用于数据选择，日期选择，时间选择。 
- storage：本地缓存
- stream：发送网络请求
- websocket：与服务器之间的交互，避免了之前的http的长轮询
- webview：提供了一系列的 `<web>` 组件操作接口 ，一般与`web`组件一起使用。 

```javascript
stream.feth({
    method:'GET',
    url:'http://www.url.com/api',
    type:'json',
    body:'只支持字符串，如果是json需使用JSON.stringify'
},function(res){
    if(res.ok){
        console.log(res.data);
    }
})
```



## 5. 通用事件

### 5.1 page

- viewapper：页面就要显示或配置的任何页面动画被执行前触发，例如，当调用 `navigator` 模块的 `push` 方法时，该事件将会在打开新页面时被触发

- viewdisapper：在页面就要关闭时被触发。

- 与组件apper和disapper不同，viewapper、viewdisapper关注的是整个页面的状态，它必须绑定到根元素上

- 特殊情况下，这两个事件也能被绑定到非根元素的body组件上，例如`wxc-navpage`组件。









