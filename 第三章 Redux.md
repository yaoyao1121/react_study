#  第三章 Redux
#### 1.Redux的基本原则

```
Flux的基本原则是“单向数据流”，redux是管理State的一个东东，所有State都需要经过redux来操作
Redux在此基础上强调三个基本原则：
 唯一数据源：指的是应用的状态数据应该只存储在唯一的一个Store上
 保持状态只读：指不能直接修改状态，要修改Store的状态，必须要通过派发一个action对象完成
 数据改变只能通过纯函数完成：纯函数就是指Reducer
```
#### 2..Redux的基本概念（ Action   Reducer  Store）
**Action**

```
概念：是把数据从应用传到Store的有效载荷。它是store数据的唯一来源。用法通过store.dispatch()把action传到store.
作用：1.用Action来分辨具体执行动作   2.操作数据首先的得到数据
```
**Reducer**
```
概念：Action只是描述了有事情发生这一事实，并没有指明应用如何更新state。这是reducer要做的事情。
通俗解释：Action就像leader,告诉我们应该做哪些事儿，并且给我们提供资源，真正干活的是苦逼的Reducer。
```
**Store**

```
 一个应用只能有一个Store
 概念：Store就是把action和reducers联系起来的对象，Store有以下职责：
  · 维持应用的 state ；
  · 提供 getState() 方法获取 state ;
  · 提供 dispatch(action) 方法更新 state ;
  · 通过 subscribe(listener) 注册监听器 。 
```
#### 3.Redux实例
在src目录下定义ActionTypes.js，代码如下：

```
//定义事件类型 
export const NEWADD = 'newAdd';
export const REDUCE = 'reduce';
```

在src目录下定义Actions.js，代码如下：

```
//定义事件
export const newAdd = (counterCaption) => {
  return{
    type: ActionTypes.NEWADD,
    counterCaption: counterCaption
  }
};
export const reduce = (counterCaption) => {
  return{
    type: ActionTypes.REDUCE,
    counterCaption: counterCaption
  }
};
```
在src目录下定义Store.js 仓库 ，代码如下：

```
import {createStore} from 'redux';
import reducer from './Reducer.js';
const initValues={
    'First':0,
    'Second':10,
    'Third':20
}
const store = createStore(reducer,initValues); //创建仓库
//createStore函数是Redux库提供的，第一个参数代表更新状态，第二个参数代表初始值，第三个参数可选，代表Store Enhancer
export default store;
```
在src目录下定义Reducer.js 仓库 ，代码如下：
import * as ActionTypes from './ActionTypes.js';

```
export default (state,action) =>{
    const {counterCaption}=action;
    switch(action.type){
        case ActionTypes.NEWADD:
            return {...state,[counterCaption]:state[counterCaption]+1};
        case ActionTypes.REDUCE:
            return {...state,[counterCaption]:state[counterCaption]-1};
        default :
            return state
    }
}
//代码中使用了三个点组成的扩展操作符，这个表示把state中所有字段扩展开，而后面对counterCaption 值对应的字段会赋上新值
```

在src/views目录下，创建CounterParent.js  代码如下：

```
import React , {Component} from "react";
import Counterson from "./Counterson.js";
import Summary from "./Summary.js";
const style ={
    margin:'20px'   //定义样式
}
class CounterParent extends Component{
    render(){
        return(
            <div style={style}>
               <Counterson caption="First"/>
               <Counterson caption="Second"/>
               <Counterson caption="Third"/>
               <hr/>
               <Summary />
            </div>
        )
    }
}
export default CounterParent
```

在src/views目录下，创建Counterson.js  代码如下：

```
import React,{Component} from 'react';
import store from '../Store.js' //引入仓库
import * as Actions from '../Actions.js'; //引入事件 Actions分发事件

class Counterson extends Component {
    constructor(props){
        super(props);
        this.newAdd = this.newAdd.bind(this);
        this.reduce = this.reduce.bind(this);
        this.onChage = this.onChage.bind(this); //定义事件方法
        this.state=this.getOwnState(); //用 getOwnState() 函数  从仓库获取初始值 
    }
    getOwnState(){
        return{
            value:store.getState()[this.props.caption] //用getState()方法 从Store仓库拿取state值
        }
    }
    onChage(){
        this.setState(this.getOwnState()) //改变的时候重新设置值
    }
    newAdd(){
        store.dispatch(Actions.newAdd(this.props.caption))  //用dispatch触发事件
    }
    reduce(){
        store.dispatch(Actions.reduce(this.props.caption)) //减去
    }
    shouldComponentUpdate(nextProps, nextState) {
	    //是react提供了组件生命周期函数，组件在决定重新渲染（虚拟dom比对完毕生成最终的dom后）之前会调用该函数
	    //该函数将是否重新渲染的权限交给了开发者，该函数默认直接返回true，表示默认直接出发dom更新
	    //这里指当caption或者count发生变化时重新渲染
        return (nextProps.caption !== this.props.caption) || (nextState.count !== this.state.count);
    }
    componentDidMount(){
        //React组件的生命周期函数 render之后被调用，并且仅调用一次
        store.subscribe(this.onChage); //注册监听器
    }
    componentWillUnmount(){
        // React组件的生命周期函数 组件被卸载前调用
        store.unsubscribe(this.onChage); //卸载监听器
    }
    render(){
        const {caption} =this.props;
        const value=this.state.value;
        return(
            <div>
                <button  onClick = {this.newAdd}>+</button>
                <button  onClick = {this.reduce}>-</button>
                <span>
                    {caption} Count :{value}
                </span>
            </div>
        )
    }
}
export default Counterson;
```
在src/views目录下，创建Summary.js  代码如下：

```
import React,{Component} from 'react';
import store from '../Store.js';
class Summary extends Component{
    constructor(props){
        super(props);
        this.onChange=this.onChange.bind(this);
        this.state=this.getOwnState();
    }
    onChange(){
        this.setState(this.getOwnState())
    }
    getOwnState(){
        const state=store.getState();
        let sum=0;
        for(const key in state){
            if(state.hasOwnProperty(key)){
                sum +=state[key];
            }
        }
        return {sum:sum};
    }
    shouldComponentUpdate(nextProps,nextState){
        return nextState.sum!==this.state.sum;
    }
    componentDidMount(){
        store.subscribe(this.onChange)
        //添加一个变化监听器，每当 dispatch action 的时候就会执行，state 树中的一部分可能已经变化。
        //你可以在回调函数里调用 getState() 来拿到当前 state。函数返回一个解绑的函数。
    }
    componentWillUnmount(){
        store.unsubscribe(this.onChange)
    }
    render(){
        const sum = this.state.sum;
        return(
            <div>Total Count:{sum}</div>
        )
    }
}
export default Summary;
```
在src目录的index.js文件中注册，代码如下：

```
import React from 'react';
import ReactDOM from 'react-dom';
import CounterParent from "./views/CounterParent"
ReactDOM.render(<CounterParent />, document.getElementById('root'));
```
#### 3.容器组件和傻瓜组件（父子关系）

```
容器组件：负责和Redux Store打交道的组件，处于外层
傻瓜组件：只专心负责渲染界面的组件，处于内层，也叫做展示组件，实质上就是一个纯函数，根据props产生结果
```
拆分容器组件和傻瓜组件，实际上，让傻瓜组件无状态，是我们拆分的主要目的之一，傻瓜组件指需要根据props来渲染结果，不需要state。 然而转台全部都交给容器组件去打点，这是它的责任。容器组件通过props传递给傻瓜组件。

把上面的代码进行改进，实际上只有视图部分代码改变，再试图代码src/views/Counterson.js 中定义两个组件Counter.js（傻瓜组件）和CounterContainer（容器组件）代码如下：

```
import React,{Component} from 'react';
import store from '../Store.js'
import * as Actions from '../Actions.js';
const buttonStyle = {
    margin: '10px'
  };
  //傻瓜组件Counter代码逻辑前所未有的简单，只有一个render函数，可以看到Counter组件完全没有state，只有一个render方法，所有数据都来自于props，这种组件叫做“无状态”组件
  class Counter extends Component {
    render() {
      const {caption, newAdd, reduce, value} = this.props;
  
      return (
        <div>
          <button style={buttonStyle} onClick={newAdd}>+</button>
          <button style={buttonStyle} onClick={reduce}>-</button>
          <span>{caption} count: {value}</span>
        </div>
      );
    }
  }
  
//   Counter.propTypes = {
//     caption: PropTypes.string.isRequired,
//     newAdd: PropTypes.func.isRequired,
//     reduce: PropTypes.func.isRequired,
//     value: PropTypes.number.isRequired
//   };
  
  //CounterContainer 组件承担了所有和Store关联的工作，它的render函数就是渲染傻瓜组件Counter而已，只负责传递必要的prop
  class CounterContainer extends Component {
    constructor(props) {
      super(props);
  
      this.newAdd = this.newAdd.bind(this);
      this.reduce = this.reduce.bind(this);
      this.onChange = this.onChange.bind(this);
      this.getOwnState = this.getOwnState.bind(this);
  
      this.state = this.getOwnState();
    }
  
    getOwnState() {
      return {
        value: store.getState()[this.props.caption]
      };
    }
  
    newAdd() {
      store.dispatch(Actions.newAdd(this.props.caption));
    }
  
    reduce() {
      store.dispatch(Actions.reduce(this.props.caption));
    }
  
    onChange() {
      this.setState(this.getOwnState());
    }
  
    shouldComponentUpdate(nextProps, nextState) {
      return (nextProps.caption !== this.props.caption) || (nextState.value !== this.state.value);
    }
  
    componentDidMount() {
      store.subscribe(this.onChange);
    }
  
    componentWillUnmount() {
      store.unsubscribe(this.onChange);
    }
  
    render() {
      return <Counter caption={this.props.caption}
        newAdd={this.newAdd}
        reduce={this.reduce}
        value={this.state.value} />
    }
  }
  
//   CounterContainer.propTypes = {
//     caption: PropTypes.string.isRequired
//   };
  
  export default CounterContainer;
  //可以看到，这个文件export导出的不再是Counter组件，认识CounterContainer组件，也就是说对于使用这个试图的模块来说，根本不会感受到傻瓜组件的存在，从外部看到的就只是容器组件。
```

