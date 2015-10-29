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
一个或多个`Routers` 或 `PlainRoutes`.当 history 改变时，`<Router>`将匹配`Routes`分支，然后渲染它已配置的 components，子路由组件将嵌入父组件。  

##### `routes`
`children`的别名。  

##### `history`
用此 history 的时候应该从 `history`包监听。

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
此方法用于从`Link`s转换为对象或者调用`transitionTo`成一个URL查询字符串。

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

当 route 所链接处被激活时将自动应用`activeClassName`或`activeStyle`.

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
当路由匹配 URL 时，单个组件将被渲染。它可以被带有 `this.props.children`的父级路由渲染。

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
Same as `component` but asynchronous, useful for
code-splitting.

###### `callback` signature
`cb(err, component)`

```js
<Route path="courses/:courseId" getComponent={(location, cb) => {
  // do asynchronous stuff to find the components
  cb(null, Course)
}}/>
```

##### `getComponents(location, callback)`
Same as `components` but asynchronous, useful for
code-splitting.

###### `callback` signature
`cb(err, components)`

```js
<Route path="courses/:courseId" getComponent={(location, cb) => {
  // do asynchronous stuff to find the components
  cb(null, {sidebar: CourseSidebar, content: Course})
}}/>
```

##### `children`
Routes can be nested, `this.props.children` will contain the element created from the child route component. Please refer to the [Route Configuration](/docs/guides/basics/RouteConfiguration.md) since this is a very critical part of the router's design.

##### `onEnter(nextState, replaceState)`
Called when a route is about to be entered. It provides the next router state and a function to redirect to another path.

##### `onLeave()`
Called when a route is about to be exited.



## PlainRoute
A plain JavaScript object route definition. `Router` turns JSX `<Route>`s into these objects, but you can use them directly if you prefer. All of the props are the same as `<Route>` props, except those listed here.

#### Props
##### `childRoutes`
An array of child routes, same as `children` in JSX route configs.

##### `getChildRoutes(location, callback)`
Same as `childRoutes` but asynchronous and receives the `location`. Useful for code-splitting and dynamic route matching (given some state or session data to return a different set of child routes).

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
A `<Redirect>` sets up a redirect to another route in your application to maintain old URLs.

#### Props
##### `from`
The path you want to redirect from, including dynamic segments.

##### `to`
The path you want to redirect to.

##### `query`
By default, the query parameters will just pass through but you can specify them if you need to.

```js
// Say we want to change from `/profile/123` to `/about/123`
// and redirect `/get-in-touch` to `/contact`
<Route component={App}>
  <Route path="about/:userId" component={UserProfile}/>
  {/* /profile/123 -> /about/123 */}
  <Redirect from="profile/:userId" to="about/:userId" />
</Route>
```

Note that the `<Redirect>` can be placed anywhere in the route hierarchy, though [normal precedence](/docs/guides/basics/RouteMatching.md#precedence) rules apply. If you'd prefer the redirects to be next to their respective routes, the `from` path will match the same as a regular route `path`.

```js
<Route path="course/:courseId">
  <Route path="dashboard" />
  {/* /course/123/home -> /course/123/dashboard */}
  <Redirect from="home" to="dashboard" />
</Route>
```



## IndexRoute
Index Routes allow you to provide a default "child" to a parent
route when visitor is at the URL of the parent, they provide convention
for `<IndexLink>` to work.

Please see the [Index Routes guide](/docs/guides/basics/IndexRoutes.md).

#### Props
All the same props as [Route](#route) except for `path`.

##### `getIndexRoute(location, callback)`
Same as `IndexRoute` but asynchronous, useful for
code-splitting.

###### `callback` signature
`cb(err, component)`

```js
<Route path="courses/:courseId" getIndexRoute={(location, cb) => {
  // do asynchronous stuff to find the index route
  cb(null, Index)
}}/>
```


## IndexRedirect
Index Redirects allow you to redirect from the URL of a parent route to another
route. They can be used to allow a child route to serve as the default route
for its parent, while still keeping a distinct URL.

Please see the [Index Routes guide](/docs/guides/basics/IndexRoutes.md).

#### Props
All the same props as [Redirect](#redirect) except for `from`.



## Handler Components
A route's handler component is rendered when that route matches the URL. The router will inject the following properties into your component when it's rendered:

#### `isTransitioning`
A boolean value that is `true` when the router is transitioning, `false` otherwise.

#### `location`
The current [location](https://github.com/rackt/history/blob/master/docs/Location.md).

#### `params`
The dynamic segments of the URL.

#### `route`
The route that rendered this component.

#### `routeParams`
A subset of `this.props.params` that were directly specified in this component's route. For example, if the route's path is `users/:userId` and the URL is `/users/123/portfolios/345` then `this.props.routeParams` will be `{userId: '123'}`, and `this.props.params` will be `{userId: '123', portfolioId: 345}`.

#### `children`
The matched child route elements to be rendered.

##### Example
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
When a route has multiple components, the child elements are available by name on `this.props.children`. All route components can participate in the nesting.

#### Example
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

## Lifecycle Mixin
Adds a hook to your component instance that is called when the router is about to navigate away from the route the component is configured on, with the opportunity to cancel the transition. Mostly useful for forms that are partially filled out.

On standard transitions, `routerWillLeave` receives a single argument: the `location` we're transitioning to. To cancel the transition, return false.

To prompt the user for confirmation, return a prompt message (string). `routerWillLeave` does not receive a location object during the beforeunload event in web browsers (assuming you're using the `useBeforeUnload` history enhancer). In this case, it is not possible for us to know the location we're transitioning to so `routerWillLeave` must return a prompt message to prevent the user from closing the tab.

#### Lifecycle Methods
##### `routerWillLeave(nextLocation)`
Called when the router is attempting to transition away from the route that rendered this component.

##### arguments
- `nextLocation` - the next location



## History Mixin
Adds the router's `history` object to your component instance.

**Note**: You do not need this mixin for route components, it's already available as `this.props.history`. This is for components deeper in the render tree that need access to the router's `history` object.

#### Methods
##### `pushState(state, pathname, query)`
Transitions to a new URL.

###### arguments
- `state` - the location state.
- `pathname` - the full URL with or without the query.
- `query` - an object that will be stringified by the router.

##### `replaceState(state, pathname, query)`
Replaces the current URL with a new one, without affecting the length of the history (like a redirect).

###### arguments
- `state` - the location state.
- `pathname` - the full URL with or without the query.
- `query` - an object that will be stringified by the router.

##### `go(n)`
Go forward or backward in the history by `n` or `-n`.

##### `goBack()`
Go back one entry in the history.

##### `goForward()`
Go forward one entry in the history.

##### `createPath(pathname, query)`
Stringifies the query into the pathname, using the router's config.

##### `createHref(pathname, query)`
Creates a URL, using the router's config. For example, it will add `#/` in front of the `pathname` for hash history.

##### `isActive(pathname, query, indexOnly)`
Returns `true` or `false` depending on if the current path is active. Will be true for every route in the route branch matched by the `pathname` (child route is active, therefore parent is too), unless `indexOnly` is specified, in which case it will only match the exact path.

###### arguments
- `pathname` - the full URL with or without the query.
- `query` - if specified, an object containing key/value pairs that must be active in the current query - explicit `undefined` values require the corresponding key to be missing or `undefined` in the current query
- `indexOnly` - a boolean (default: `false`).

#### Examples
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

Let's say you are using bootstrap and want to get `active` on those `li` tags for the Tabs:

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

#### But I’m using Classes
> Notice how we never told you to use ES6 classes? :)

https://twitter.com/soprano/status/610867662797807617

If you aren't happy using `createClass` for a handful of components in your app for the sake of the `History` mixin, have a couple of options:

- Pass `this.props.history` from your route components down to the components that need it.
- Use context

```js
import { PropTypes } from 'react-router'

class MyComponent extends React.Component {
  doStuff() {
    this.context.history.pushState(null, '/some/path')
  }
}

MyComponent.contextTypes = { history: PropTypes.history }
```

- [Make your history a module](/docs/guides/advanced/NavigatingOutsideOfComponents.md)
- Create a higher order component, we might end up shipping with this and deprecating history, just haven't had the time to think it through all the way.

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
The RouteContext mixin provides a convenient way for route components to set the route in context. This is needed for routes that render elements that want to use the [Lifecycle mixin](#lifecycle) to prevent transitions.

It simply adds `this.context.route` to your component.



## Utilities

## `useRoutes(createHistory)`
Returns a new `createHistory` function that may be used to create history objects that know about routing.

- listen((error, nextState) => {})
- listenBeforeLeavingRoute(route, (nextLocation) => {})
- match(location, (error, redirectLocation, nextState) => {})
- isActive(pathname, query, indexOnly=false)


## `match(location, cb)`

This function is to be used for server-side rendering. It matches a set of routes to a location, without rendering, and calls a `callback(error, redirectLocation, renderProps)` when it's done.

*Note: You probably don't want to use this in a browser. Use the [`history.listen`](https://github.com/rackt/history/blob/master/docs/GettingStarted.md#getting-started) API instead.*



## `createRoutes(routes)`

Creates and returns an array of routes from the given object which may be a JSX route, a plain object route, or an array of either.

#### params
##### `routes`
One or many [`Routes`](#route) or [`PlainRoutes`](#plainRoute).


## PropTypes
Coming so soon!

