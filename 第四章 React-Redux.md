# 第四章 React-Redux
#### 1.组件Context

```
概念：Context是react提供的实现上下文传值的组件。让一个树状组件上所有组件都能访问一个共同的对象，为了完成这个任务，需要上级组件和下级组件配合。
	首先，上级组件要宣称自己支持context，并且提供一个函数来返回代表Context的对象。
	然后，这个上级组件之下的所有子孙组件，只要宣称自己需要这个context，就可以通过this.context访问到这个共同的环境对象。

```
注意，React Context 也有一些局限性：

```
1.React Context 目前在 React 在是一个 experimental feature，在未来的版本中会有变化说不定，这点官方文档有说明。
2.在组件树中，如果中间某一个组件 ShouldComponentUpdate returning false 了，会阻碍 context 的正常传值，导致子组件无法获取更新。
3.组件本身 extends React.PureComponent 也会阻碍 context 的更新。

解决 ShouldComponentUpdate 与 Context 之间冲突的方案也是有的，例如使用 Redux 或者 Mobx 等全局单一状态管理。这里抛砖引玉，介绍一种简单的解决 Context 不起作用的方法，它必须满足两个条件：

1.Context 应该是唯一不可变的
2.组件只在初始化的时候去获取 Context
```
#### 2.组件Context案例





















