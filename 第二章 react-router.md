#  第二章 react-router
**首先，确认已安装react-router   命令   npm i react-router --save-dev  安装到生产环境**
#### 1.路由链接和嵌套
首先，在src目录下创建components文件夹下面创建通用的TopMenu.js文件，代码如下：

```
import React from 'react';
import {Link} from 'react-router'; //解析Link组件
const view=()=> {
    return(
        <div>
            <ul>
                <li><Link to="/home">Home</Link></li>
               //Link组件的to属性指向一个路径，对应路径在src/Routes.js中应有定义，注意前面有/，代表从根路径开始匹配
                <li><Link to="/about">About</Link></li>
            </ul>
        </div>
    )
}
export {view}
//可以直接在Home About NotFound组件中直接引用TopMenu组件，但是若将来要用另外一个组件替换掉TopMenu组件，又必须要修改每一个文件，故使用路由嵌套功能解决此问题
```
在src/pages/App.js中定义个新的React组件App，这个组件包含TopMenu组件，同时也会渲染出Home、About或者NotFound页面，代码如下：

```
import React from 'react';
import {view as TopMenu} from '../components/TopMenu.js'
const App =({children})=>{
    return(
        <div>
            <TopMenu />
            <div>{children}</div> //children代表子组件  Home About  NotFound 
        </div>
    )
}
export default App;
```
在src/Routers.js

```
import React, { Component } from 'react';
import {Router,Route,hashHistory,IndexRoute} from 'react-router'
import Home from "./pages/Home.js"
import About from "./pages/About.js";
import NotFound from "./pages/NotFound.js";
import App from "./pages/App.js"
//browserHistory 和 hashHistory 区别 https://www.cnblogs.com/liuna/p/6137970.html
class Routes extends Component{
    render(){
        return(
            <Router history={hashHistory}>
                <Route path="/" component ={App}>
	                <IndexRoute component={Home} /> //默认链接
                    <Route path="home" component={Home} />
                    <Route path="about" component={About} />
                    <Route path="*" component={NotFound} />
                </Route>  
                //IndexRoute代表一个Route下的默认路由，
                //这样一来无论http://localhost:3000还是http://localhost:3000/home访问都是包含Home主页内容的
            </Router>
        )
    }
}
export default Routes;
```
更多路由详情清查看  https://blog.csdn.net/haoshidai/article/details/52755193