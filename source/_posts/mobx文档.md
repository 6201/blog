---
title: mobx文档 
date: 2018-04-03 23:54:05  
tags:  
- 记录  
categories: 编程
---

### MobX  
简单、可扩展的状态管理  
<!--more-->
* 安装：`npm install mobx --save` 。 React绑定库： ` npm install mobx-react --save`。要启用ESNext的装饰器（可选），参见下面。  
* CDN
* [https://unpkg.com/mobx/lib/mobx.umd.js](https://unpkg.com/mobx/lib/mobx.umd.js)  
* [https://cdnjs.com/libraries/mobx](https://cdnjs.com/libraries/mobx)  

  
### MobX 要点  

到目前为止，这一切都可能听起来有点花哨，但使用 MobX 将一个应用变成响应式的可归纳为以下三个步骤:  

### 1. 定义状态并使其可以观察  
可以用任何你喜欢的数据结构来存储状态，如对象、数组、类。 循环数据结构、引用，都没有关系。 只要确保所有会随时间流逝而改变的属性打上 `mobx` 的标记使它们变得可观察即可。  

{% codeblock lang:javascript %}
import {observable} from 'mobx';

var appState = observable({
    timer: 0
});
{% endcodeblock %}





### 2. 创建视图以响应状态的变化  
我们的 `appState` 还没有观察到任何的东西。 你可以创建视图，当 `appState` 中相关数据发生改变时视图会自动更新。 MobX 会以一种最小限度的方式来更新视图。 事实上这一点可以节省了你大量的样板文件，并且它有着令人匪夷所思的高效。  

通常来说，任何函数都可以成为可以观察自身数据的响应式视图，MobX 可以在任何符合ES5的JavaScript环境中应用。 但是在这所用的示例是 ES6版本的 React 组件视图。  

{% codeblock lang:JSX %}
import {observer} from 'mobx-react';

@observer
class TimerView extends React.Component {
    render() {
        return (<button onClick={this.onReset.bind(this)}>
                Seconds passed: {this.props.appState.timer}
            </button>);
    }

    onReset () {
        this.props.appState.resetTimer();
    }
};

ReactDOM.render(<TimerView appState={appState} />, document.body); 
{% endcodeblock %}



### 3. 更改状态  
第三件要做的事就是更改状态。 也就是你的应用究竟要做什么。 不像一些其它框架，MobX 不会命令你如何如何去做。 这是最佳实践，但关键要记住一点: **MobX 帮助你以一种简单直观的方式来完成工作**。

下面的代码每秒都会修改你的数据，而当需要的时候UI会自动更新。 无论是在改变状态的控制器函数中，还是在应该**更新**的视图中，都没有明确的关系定义。 使用 `observable` 来装饰你的**状态**和**视图**，这足以让 MobX检测所有关系了。  


{% codeblock lang:javascript %}
appState.resetTimer = action(function reset() {
    appState.timer = 0;
});

setInterval(action(function tick() {
    appState.timer += 1;
}), 1000);
{% endcodeblock %}


只有在严格模式(默认是不启用)下使用 MobX 时才需要 `action` 包装。 建议使用 `action`，因为它将帮助你更好地组织应用，并表达出一个函数修改状态的意图。 同时,它还自动应用事务以获得最佳性能。  
#### 开启严格模式  
4.0.0 changelog 中：
`useStrict(boolean)` was **dropped**, use `configure({ enforceActions: boolean })` instead

