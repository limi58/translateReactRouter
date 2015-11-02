# react-router API 中文翻译
一个一个词手工翻译的，只为熟悉文档，因此建议不以此作参考，具体请参考[官方文档](https://github.com/rackt/react-router/blob/master/docs/API.md)  
[米不过分](http://www.imbgf.com)

# API Reference

* [Components](#components)
  - [Router](#router)
  - [Link](#link)
  - [IndexLink](#indexlink)
  - [RoutingContext](#routingcontext)

* [Configuration Components](#configuration-components)
  - [Route](#route)
  - [PlainRoute](#plainroute)
  - [Redirect](#redirect)
  - [IndexRoute](#indexroute)
  - [IndexRedirect](#indexredirect)

* [Handler Components](#handler-components)

* [Mixins](#mixins)
  - [Lifecycle](#lifecycle-mixin)
  - [History](#history-mixin)
  - [RouteContext](#routecontext-mixin)

* [Utilities](#utilities)
  * [useRoutes](#useroutescreatehistory)
  * [createRoutes](#createroutesroutes)
  * [PropTypes](#proptypes)


## Components

### Router
React Router 的主要组件，他可以让你的 UI 和 URL 保持同步。

#### Props
##### `children` (必需) 
一个或多个 `Routers` 或 `PlainRoutes`.当 history 改变时，`<Router>` 将匹配 `Routes` 分支，然后渲染它已配置的 components，子路由组件将嵌入父组件。  

##### `routes`
`children` 的别名。  

##### `history`
用此 history 的时候应该从 `history` 包监听。

##### `createElement(Component, props)`
当 router 准备去渲染一个 route 组件分支的时候，它将用这个方法去创建一个元素。当你使用了一些数据抽象的时候，你可能想去控制它去创建元素，比如设置 stores 的订阅，或者从每个组件的 props 传入一些应用程序模块。  

```js
<Router createElement={createElement} />

// 默认行为
function createElement(Component, props) {
  // 确保你传入了所有props！
  return <Component {...props}/>
}

// 也许你用了像Relay的东西
function createElement(Component, props) {
  // 确保你传入了所有props！
  return <RelayContainer Component={Component} routerProps={props}/>
}
```

##### `stringifyQuery(queryObject)`
此方法用于从 `Link`s 转换为对象或者调用 `transitionTo` 成一个URL查询字符串。

##### `parseQueryString(queryString)`
此方法用于转换一个查询字符串成一个传递到 route 组件的 props 的对象。

##### `onError(error)`
当 router 匹配时，错误也许也出现了，这时这是一个去捕捉和处理他们的机会。通常这些来自异步特性，像： `route.getComponents, route.getIndexRoute, and route.getChildRoutes`.

##### `onUpdate()`
当 router 更新他的 state 去响应 URL 变化时可以去调用。

#### Examples
你可以去 [`examples/`](/examples) 目录查看大量使用 `Router` 的例子。



### Link
这是主要的方法来允许用户浏览你的应用. `<Link>` 将渲染一个可访问的带有指定的 href 锚标签。

当 route 所链接处被激活时将自动应用 `activeClassName` 或 `activeStyle` .

#### Props
##### `to`
要链接到的路径，例如 `/users/123`.

##### `query`
一个对象的键值对将被序列化。

##### `hash`
一个要放到 URL 的 hash，例如 `#a-hash`.

##### `state`
要存留在 `location` 的状态。

##### `activeClassName`
当 `<Link>` 被激活的时候的类名。 没有激活时将保持默认。

##### `activeStyle`
当 route 被激活的时候要应用的样式。

##### `onClick(e)`
一个提供给点击事件的自定义处理器。 就像在 `<a>` 标签工作一样 - 调用 `e.preventDefault()` 将组织转变事件发生, 当调用 `e.stopPropagation()` 将阻止冒泡事件。

##### *others*
你可以传入你喜欢的属性在 `<a>` 上比如： `title`, `id`, `className`, 等等。

#### Example
提供一个 route 如 `<Route path="/users/:userId" />`:

```js
<Link to={`/users/${user.id}`} activeClassName="active">{user.name}</Link>
// becomes one of these depending on your History and if the route is
// 激活时
<a href="/users/123" class="active">Michael</a>
<a href="#/users/123">Michael</a>

// 改变 activeClassName
<Link to={`/users/${user.id}`} activeClassName="current">{user.name}</Link>

// 当链接激活时改变样式
<Link to="/users" style={{color: 'white'}} activeStyle={{color: 'red'}}>Users</Link>
```

### IndexLink
文档正在制作！

### RoutingContext
文档正在制作！



## 组件配置

## Route
`Route` 用来在你的应用组件层级里声明 routes 映射

#### 属性
##### `path`
用在 URL.

它将和父路由的 path 合并除非以 `/`开始,这将用于绝对路径.

**注意**: 绝对路径可能不能用在动态路由配置里 [dynamically loaded](https://github.com/rackt/react-router/blob/master/docs/guides/advanced/DynamicRouting.md).

如果左边为 undefined，路由器将试图匹配子路由.

##### `component`
当路由匹配 URL 时，单个组件将被渲染。它可以被带有 `this.props.children` 的父级路由渲染。

```js
const routes = (
  <Route component={App}>
    <Route path="groups" component={Groups}/>
    <Route path="users" component={Users}/>
  </Route>
)

class App extends React.Component {
  render () {
    return (
      <div>
        {/* this will be either <Users> or <Groups> */}
        {this.props.children}
      </div>
    )
  }
}
```

##### `components`
当 path 匹配 URL 时，以对象 `name:component` 定义的多个路由组件将被渲染。他们会被带有
 `this.props.children[name]` 的父级路由组件渲染。

```js
// think of it outside the context of the router, if you had pluggable
// portions of your `render`, you might do it like this
<App children={{main: <Users/>, sidebar: <UsersSidebar/>}}/>

// So with the router it looks like this:
const routes = (
  <Route component={App}>
    <Route path="groups" components={{main: Groups, sidebar: GroupsSidebar}}/>
    <Route path="users" components={{main: Users, sidebar: UsersSidebar}}>
      <Route path="users/:userId" component={Profile}/>
    </Route>
  </Route>
)

class App extends React.Component {
  render () {
    const { main, sidebar } = this.props.children
    return (
      <div>
        <div className="Main">
          {main}
        </div>
        <div className="Sidebar">
          {sidebar}
        </div>
      </div>
    )
  }
}

class Users extends React.Component {
  render () {
    return (
      <div>
        {/* if at "/users/123" `children` will be <Profile> */}
        {/* UsersSidebar will also get <Profile> as this.props.children,
            so its a little weird, but you can decide which one wants
            to continue with the nesting */}
        {this.props.children}
      </div>
    )
  }
}
```
##### `getComponent(location, callback)`
和 `component` 一样，但是是异步的，有助于代码分割.

###### `callback` signature
`cb(err, component)`

```js
<Route path="courses/:courseId" getComponent={(location, cb) => {
  // 为了去找组件去做一些异步的东东
  cb(null, Course)
}}/>
```

##### `getComponents(location, callback)`
和 `components` 一样，但是是异步的，有助于代码分割.

###### `callback` signature
`cb(err, components)`

```js
<Route path="courses/:courseId" getComponent={(location, cb) => {
  // 为了去找组件去做一些异步的东东
  cb(null, {sidebar: CourseSidebar, content: Course})
}}/>
```

##### `children`
路由可以被嵌套, `this.props.children` 将从子路由组件去包含元素. 请参考 [Route Configuration](/docs/guides/basics/RouteConfiguration.md) 因为这个是个非常重要的路由设计部分.

##### `onEnter(nextState, replaceState)`
在路由即将进入时被调用. 它提供下一个路由器的状态和重定向到其他路径的方法.

##### `onLeave()`
在路由即将离开的时候被调用.



## PlainRoute
用纯 javascript 对象的方式去定义路由. `Router` 会转换 JSX `<Route>`s 为这些对象, 不过你喜欢的话可以直接用它. 它所有的属性和 `<Route>` 一样, 除了下面列出的.

#### 属性
##### `childRoutes`
一个路由的数组, 在 jsx 和 `children` 一样.

##### `getChildRoutes(location, callback)`
和 `childRoutes` 相同，但是是异步的而且接受参数 `location`. 有助于代码分割和动态路由匹配 (提供一些状态或者会话数据去返回一个子路由).

###### `callback` signature
`cb(err, routesArray)`

```js
let myRoute = {
  path: 'course/:courseId',
  childRoutes: [
    announcementsRoute,
    gradesRoute,
    assignmentsRoute
  ]
}

// async child routes
let myRoute = {
  path: 'course/:courseId',
  getChildRoutes(location, cb) {
    // do asynchronous stuff to find the child routes
    cb(null, [ announcementsRoute, gradesRoute, assignmentsRoute ])
  }
}

// navigation dependent child routes
// can link with some state
<Link to="/picture/123" state={{ fromDashboard: true }}/>

let myRoute = {
  path: 'picture/:id',
  getChildRoutes(location, cb) {
    let { state } = location

    if (state && state.fromDashboard) {
      cb(null, [dashboardPictureRoute])
    } else {
      cb(null, [pictureRoute])
    }
  }
}
```



## Redirect
`<Redirect>` 在你的应用设置一个重定向到其他路由以维护之前的 URLs.

#### 属性
##### `from`
你想重定向的路径, 可以包含动态片段.

##### `to`
你想重定向到的路径.

##### `query`
默认情况, 查询参数将会通过不过你可以根据需要去指定他们.

```js
// 我们想改变从 `/profile/123` 到 `/about/123`
// 并且重定向 `/get-in-touch` 到 `/contact`
<Route component={App}>
  <Route path="about/:userId" component={UserProfile}/>
  {/* /profile/123 -> /about/123 */}
  <Redirect from="profile/:userId" to="about/:userId" />
</Route>
```

注意 `<Redirect>` 可以在路由层级里随意放置, 通过 [normal precedence](/docs/guides/basics/RouteMatching.md#precedence) 来应用规则. 如果你喜欢重定向到旁边的路由,  `from` 路径将匹配常规的 `path`.

```js
<Route path="course/:courseId">
  <Route path="dashboard" />
  {/* /course/123/home -> /course/123/dashboard */}
  <Redirect from="home" to="dashboard" />
</Route>
```



## IndexRoute
当访问者在父级路由的时候索引路由允许提供一个默认的 "child" 给父级路由, 它提供一个方便的 `<IndexLink>` 去工作.

请查看 [Index Routes guide](/docs/guides/basics/IndexRoutes.md).

#### 属性
所有属性和 [Route](#route) 一样除了 `path`.

##### `getIndexRoute(location, callback)`
和 `IndexRoute` 一样，不过是异步的，有助于代码分割.

###### `callback` signature
`cb(err, component)`

```js
<Route path="courses/:courseId" getIndexRoute={(location, cb) => {
  // 为了寻找索引路由去做一些异步的东东
  cb(null, Index)
}}/>
```


## IndexRedirect
索引重定向允许你从父级路由 URL 到其他路由. 它被用来允许子路由去提供默认路由给父级，然而仍然保持不同的 URL.

具体请查看 [Index Routes guide](/docs/guides/basics/IndexRoutes.md).

#### 属性
和 [Redirect](#redirect) 一样除了 `from`.



## Handler Components
当路由匹配 URL 的时候，路由处理器组件将被渲染. 当其渲染的时候将允许以下的属性注入:

#### `isTransitioning`
当路由器转变的时候，布尔值为 `true` , `false` 同理.

#### `location`
当前的 [location](https://github.com/rackt/history/blob/master/docs/Location.md).

#### `params`
URL 的动态片段.

#### `route`
被渲染的组件的路由.

#### `routeParams`
直接指定这个组件的路由 `this.props.params` 的子集. 举个例子, 如果你路由路径是 `users/:userId` 并且 URL 是 `/users/123/portfolios/345` 然后 `this.props.routeParams` 将为 `{userId: '123'}`, 并且 `this.props.params` 将为 `{userId: '123', portfolioId: 345}`.

#### `children`
匹配将被渲染的子路由元素.

##### 例子
```js
render((
  <Router history={history}>
    <Route path="/" component={App}>
      <Route path="groups" component={Groups} />
      <Route path="users" component={Users} />
    </Route>
  </Router>
), node)

class App extends React.Component {
  render() {
    return (
      <div>
        {/* this will be either <Users> or <Groups> */}
        {this.props.children}
      </div>
    )
  }
}
```

### Named Components
当路由有多个组件的时候, 命名为 `this.props.children` 的子元素将可用. 在嵌套里所有路由组件将会一起参与.

#### 例子
```js
render((
  <Router>
    <Route path="/" component={App}>
      <Route path="groups" components={{main: Groups, sidebar: GroupsSidebar}} />
      <Route path="users" components={{main: Users, sidebar: UsersSidebar}}>
        <Route path="users/:userId" component={Profile} />
      </Route>
    </Route>
  </Router>
), node)

class App extends React.Component {
  render() {
    // the matched child route components become props in the parent
    return (
      <div>
        <div className="Main">
          {/* this will either be <Groups> or <Users> */}
          {this.props.children.main}
        </div>
        <div className="Sidebar">
          {/* this will either be <GroupsSidebar> or <UsersSidebar> */}
          {this.props.children.sidebar}
        </div>
      </div>
    )
  }
}

class Users extends React.Component {
  render() {
    return (
      <div>
        {/* if at "/users/123" this will be <Profile> */}
        {/* UsersSidebar will also get <Profile> as this.props.children,
            you pick where it renders */}
        {this.props.children}
      </div>
    )
  }
}
```



## Mixins

## Mixin 生命周期
在你的组件实例上添加一个钩子，他将在路由准备改变时被调用, 可以在适当时期取消路由改变. 大多数用在填写部分表单时.

在标准转变中, `routerWillLeave` 接受一个参数: 我们要转到的 `location`. 返回 false 可以取消转变.

为了提示用户去确认，应该返回一个字符串消息. 如果浏览器处在 beforeunload 事件， `routerWillLeave` 将不会接受 location 对象 (假设你正在使用 `useBeforeUnload` history enhancer). 在此情形, 我们是不可能知道我们过渡到的位置，所以`routerWillLeave`必须返回一个提示消息,以防止用户关闭选项卡.

#### Methods 生命周期
##### `routerWillLeave(nextLocation)`
在路由器试图转变的时候被调用.

##### 参数
- `nextLocation` - 下一个地址



## History Mixin
添加一个路由器 `history` 对象在你的组件实例里.

**注意**: 你不需要给路由组件去提供一个mixin, 因为 `this.props.history` 完全可用. This is for components deeper in the render tree that need access to the router's history object..

#### 方法
##### `pushState(state, pathname, query)`
转到新的 URL.

###### 参数
- `state` - 地址状态.
- `pathname` - 完整的 URL 或者是去除参数的 URL.
- `query` - 将被路由器序列化的对象.

##### `replaceState(state, pathname, query)`
用新的地址取代当前的, 不影响 history 的长度 (像重定向).

###### 参数
- `state` - 地址状态.
- `pathname` - 完整的 URL 或者是去除参数的 URL.
- `query` - 将被路由器序列化的对象.

##### `go(n)`
在历史记录中前进或者后退，通过这个： `n` or `-n`.

##### `goBack()`
在历史记录中后退一步.

##### `goForward()`
在历史记录中前进一步.

##### `createPath(pathname, query)`
用路由器的配置序列化参数到路径名里.

##### `createHref(pathname, query)`
哟个路由器的配置创建一个 URL. 例如, 给 hash history 之前加上一个 `#/`.

##### `isActive(pathname, query, indexOnly)`
返回 `true` 或 `false` 取决于当前路径是否被激活. 在每个路由将当路由分支匹配 `pathname` 时每个这个路由都将为 true(子路由被激活了，所以父级也将被激活), 除非 `indexOnly` 被指定, 在这个情况下将只匹配精确的路径.

###### 参数
- `pathname` - 不带参数的完整路径.
- `query` - 如果指定, 一个包含键值对的对象在当前参数将被激活 - explicit `undefined` values require the corresponding key to be missing or `undefined` in the current query
- `indexOnly` - 布尔值 (默认： `false`).

#### 例子
```js
import { History } from 'react-router'

React.createClass({
  mixins: [ History ],
  render() {
    return (
      <div>
        <div onClick={() => this.history.pushState(null, '/foo')}>Go to foo</div>
        <div onClick={() => this.history.replaceState(null, 'bar')}>Go to bar without creating a new history entry</div>
        <div onClick={() => this.history.goBack()}>Go back</div>
     </div>
   )
 }
})
```

假如说你正在使用 bootstrap 并且在这些 `li` 中想得到 `active` :

```js
import { Link, History } from 'react-router'

const Tab = React.createClass({
  mixins: [ History ],
  render() {
    let isActive = this.history.isActive(this.props.to, this.props.query)
    let className = isActive ? 'active' : ''
    return <li className={className}><Link {...this.props}/></li>
  }
})

// use it just like <Link/>, and you'll get an anchor wrapped in an `li`
// with an automatic `active` class on both.
<Tab href="foo">Foo</Tab>
```

#### 但我用的是类咋办？
> 是不是注意到我们从来没有告诉你如何去用 ES6 的类? :)

https://twitter.com/soprano/status/610867662797807617

如果你不喜欢用 `createClass` 在创建一些有 `History` mixin 的组件, 试试这2个选择:

- 通过 `this.props.history` 从你的路由组件放到你需要的组件去.
- 使用 context

```js
import { PropTypes } from 'react-router'

class MyComponent extends React.Component {
  doStuff() {
    this.context.history.pushState(null, '/some/path')
  }
}

MyComponent.contextTypes = { history: PropTypes.history }
```

- [模快化 history](/docs/guides/advanced/NavigatingOutsideOfComponents.md)
- 创建一个高阶组件, 我们可以不顾 history 的创建过程.

```js
function connectHistory(Component) {
  return React.createClass({
    mixins: [ History ],
    render() {
      return <Component {...this.props} history={this.history} />
    }
  })
}

// other file
import connectHistory from './connectHistory'

class MyComponent extends React.Component {
  doStuff() {
    this.props.history.pushState(null, '/some/where')
  }
}

export default connectHistory(MyComponent)
```



## RouteContext Mixin
RouteContext mixin 提供一个方便的方法在路由组件上下文去设置路由. 这就需要一个想要使用[Lifecycle mixin](#lifecycle)去阻止跳转的渲染元素.

非常简单，只需要添加 `this.context.route` 到你的组件.



## 使用工具

## `useRoutes(createHistory)`
返回一个新的被用来创建 history 对象的 `createHistory` 函数.

- listen((error, nextState) => {})
- listenBeforeLeavingRoute(route, (nextLocation) => {})
- match(location, (error, redirectLocation, nextState) => {})
- isActive(pathname, query, indexOnly=false)


## `match(location, cb)`

此函数用在服务端渲染. It matches a set of routes to a location, without rendering, and calls a `callback(error, redirectLocation, renderProps)` when it's done.

*注意: 你不应该把它用在浏览器. 使用 [`history.listen`](https://github.com/rackt/history/blob/master/docs/GettingStarted.md#getting-started) API 去取代它.*



## `createRoutes(routes)`

创建并且返回一个可能由 JSX 路由提供的对象的路由数组. 一个纯的对象路由，或者是数组。

#### 参数
##### `routes`
一个或多个 [`Routes`](#route) 或者 [`PlainRoutes`](#plainRoute).


## PropTypes
Coming so soon!

